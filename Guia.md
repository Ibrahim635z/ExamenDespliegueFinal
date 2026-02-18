### Pasos iniciales (en la carpeta de tu proyecto)

1. **`git init`**
Inicializa un nuevo repositorio Git local en tu carpeta.
2. **`git add .`**
Agrega todos los archivos del proyecto al "área de preparación" (staging area).
3. **`git commit -m "Primer commit"`**
Registra los cambios en el historial local con un mensaje descriptivo.

### Vinculación y subida (conecta con GitHub)

4. **`git branch -M main`**
Renombra la rama principal a "main" (el estándar actual de GitHub).
5. **`git remote add origin https://github.com/usuario/nombre-del-repo.git`**
Vincula tu repositorio local con el repositorio remoto vacío que creaste en GitHub.
6. **`git push -u origin main`**
Sube tus archivos a GitHub y establece la conexión permanente entre las ramas.

---

¿Quieres que te explique cómo configurar la autenticación por SSH o Token si te da error al intentar subirlo?






# 📘 Guía de Despliegue: AWS (EC2 + EFS) + Docker + GitHub Actions

### 🎯 Objetivo

Desplegar una aplicación PHP con Docker en AWS de forma automática.

1. **Infraestructura:** EC2 (Servidor) + EFS (Disco compartido).
2. **Automatización:** GitHub Actions sube el código y reinicia Docker.

---

## 🟦 PASO 1: Crear el disco (EFS)

*El EFS servirá para guardar los archivos de forma persistente.*

1. Ve a **AWS Console** > **EFS**.
2. Dale a **Create file system**.
3. Configuración:
* **Name:** `MiDiscoEFS` (opcional).
* **VPC:** La misma que usarás luego para la EC2 (importante).
* **Availability:** Regional (Standard).


4. Dale a **Create**.
5. ⚠️ **¡IMPORTANTE!** Copia el **File System ID**.
* Ejemplo: `fs-0e281c699285c3487` (lo necesitarás en el paso 3).



---

## 🟧 PASO 2: Configurar la Seguridad (Security Groups)

*Necesitamos dos "porteros": uno para la máquina y otro para el disco.*

### A. Security Group para la EC2 (`SG-Web`)

Crea un Security Group nuevo y abre estos puertos en **Inbound Rules**:

| Tipo | Puerto | Origen (Source) | ¿Para qué sirve? |
| --- | --- | --- | --- |
| **SSH** | 22 | `0.0.0.0/0` | Conexión remota (GitHub/Tú) |
| **HTTP** | 80 | `0.0.0.0/0` | Web estándar |
| **Custom TCP** | **8080** | `0.0.0.0/0` | **Tu App Docker** |
| **HTTPS** | 443 | `0.0.0.0/0` | Web segura (opcional) |

### B. Security Group para el EFS (`SG-Disco`)

Crea otro Security Group para el disco:

| Tipo | Puerto | Origen (Source) | ¿Para qué sirve? |
| --- | --- | --- | --- |
| **NFS** | 2049 | **SG-Web** (El ID del grupo de arriba) | Permite que la EC2 monte el disco |

---

## 🟩 PASO 3: Crear la EC2 (La Máquina)

Lanza una instancia con estas características:

1. **AMI:** Ubuntu Server 24.04 LTS (o 22.04).
2. **Instance Type:** `t2.micro` o `t3.micro`.
3. **Key Pair:** Selecciona tu archivo `.pem`.
4. **Network Settings:** Selecciona el Security Group **`SG-Web`** que creaste antes.
5. **Advanced Details > User Data:**
*Copia y pega este script. (He cambiado `dnf` por `apt` para que funcione en Ubuntu).*

```bash
#!/bin/bash
set -e

# 1. Actualizar sistema e instalar herramientas NFS
sudo apt-get update -y
sudo apt-get install -y nfs-common

# 2. Instalar Docker
sudo apt-get install -y docker.io
sudo systemctl start docker
sudo systemctl enable docker
sudo usermod -aG docker ubuntu

# 3. Instalar Docker Compose
sudo curl -L "https://github.com/docker/compose/releases/download/v2.27.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose

# 4. Montar el EFS (CAMBIA EL ID ABAJO)
mkdir -p /efs
# 👇 SUSTITUYE 'fs-XXXXXXXX' POR TU ID REAL DEL PASO 1
sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport fs-XXXXXXXX.efs.us-east-1.amazonaws.com:/ /efs

# 5. Preparar carpetas
mkdir -p /home/ubuntu/app
sudo chown -R ubuntu:ubuntu /home/ubuntu/app /efs

```

> **Nota:** Al lanzar la instancia, el EFS se montará solo. No necesitas entrar por SSH para instalar nada.

---

## 🟪 PASO 4: Conectar con GitHub

En tu repositorio de GitHub, ve a **Settings > Secrets and variables > Actions** y crea estos secretos:

| Nombre Secreto | Valor a poner |
| --- | --- |
| `EC2_HOST` | La IP Pública de tu instancia (ej. `3.225.14.125`) |
| `EC2_USER` | `ubuntu` |
| `EC2_KEY` | El contenido completo de tu archivo `.pem` |

---


Configuración de Dominio en IONOS
Este es el paso para que tu dominio apunte a tu servidor AWS.

Iniciar Sesión :

Entra en tu panel de control de IONOS y ve a la sección Dominios y SSL.

Gestión de DNS:

Haz clic en el engranaje (⚙️) o en el menú de acciones de tu dominio y selecciona DNS.

Modificar el Registro A (Address):

Busca el registro de tipo A (es el que apunta el nombre del dominio a una dirección IPv4).

Si hay uno existente apuntando a una IP de IONOS, edítalo o bórralo.

Host: @ (esto significa el dominio raíz, ej. tudominio.com) y también haz otro para www si es necesario.

Apunta a (Valor): Pega aquí tu Elastic IP de AWS (ej. 54.12.34.56).

TTL (Time To Live): Puedes ponerlo en "1 hora" o lo más bajo posible para que el cambio sea rápido.

Guardar: Confirma los cambios.





## 🟥 PASO 5: El Workflow (Automático)

Crea el archivo `.github/workflows/deploy.yml` con este contenido exacto.
*Este script detecta si Docker ya está instalado y simplemente actualiza la app.*

```yaml
name: Deploy PHP App to EC2

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: 📥 Descargar repositorio
        uses: actions/checkout@v4

      - name: 📂 Copiar archivos a la EC2 (SCP)
        uses: appleboy/scp-action@v0.1.7
        with:
          host: ${{ secrets.EC2_HOST }}
          username: ${{ secrets.EC2_USER }}
          key: ${{ secrets.EC2_KEY }}
          source: "."
          target: "~/app"

      - name: 🚀 Ejecutar despliegue (SSH)
        uses: appleboy/ssh-action@v1.0.0
        with:
          host: ${{ secrets.EC2_HOST }}
          username: ${{ secrets.EC2_USER }}
          key: ${{ secrets.EC2_KEY }}
          script: |
            set -e
            
            # 1. Asegurar permisos en carpeta EFS
            echo "🔧 Configurando carpetas..."
            sudo mkdir -p /efs/app
            
            # 2. Copiar la App al volumen persistente (EFS)
            # Ajusta la ruta si tu código está en una subcarpeta
            if [ -d "~/app/app" ]; then
                sudo cp -r ~/app/app/* /efs/app/
            else
                sudo cp -r ~/app/* /efs/app/
            fi

            # 3. Reiniciar Docker
            echo "🚀 Reiniciando contenedores..."
            cd ~/app
            sudo docker-compose down || true
            sudo docker-compose up --build -d

```

---

## ✅ PASO 6: Comprobación Final

1. Haz un **git push** con tus cambios.
2. Espera a que salga el ✅ verde en GitHub Actions.
3. Abre tu navegador y entra en:

👉 **`http://TU-IP-PUBLICA:8080`**

*(Recuerda poner el `:8080` al final)*