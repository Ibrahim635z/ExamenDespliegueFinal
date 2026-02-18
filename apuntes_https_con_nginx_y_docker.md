# 🌐 Configuración de HTTPS con Nginx y Docker

Estos apuntes explican **paso a paso** cómo configurar HTTPS en un proyecto con **Nginx + PHP + Docker**, usando certificados SSL reales. Están pensados para que sean **fáciles de entender**, **ordenados** y **completos**

---

## 📁 1. Estructura de carpetas

Dentro de tu proyecto, entra en la carpeta `web` y crea una nueva carpeta llamada:

```
web/
 └── certs/
```

Esta carpeta `certs` contendrá **los certificados SSL** que permiten que la web funcione con `https://` 🔒

---

## 🔐 2. Archivos necesarios dentro de `certs`

Dentro de la carpeta `certs` deben existir **dos archivos obligatorios**:

```
certs/
 ├── certificado.crt
 └── clave.key
```

### 📄 certificado.crt

Este archivo contiene **la cadena de certificados SSL**.

**Pasos:**

1. Abre los certificados con el **Bloc de notas**.
2. Copia y pega el contenido **en este orden exacto**:
   - Primero 👉 **el certificado principal (cert)**
   - Después 👉 **los dos certificados intermedios (intermediate)**
3. Guarda todo junto dentro de `certificado.crt`.

📌 Importante: El orden es clave para que el navegador confíe en el certificado.

---

### 🔑 clave.key

Este archivo contiene **la clave privada** del certificado.

**Pasos:**

1. Abre el archivo `.key` con el Bloc de notas.
2. Copia todo su contenido.
3. Pégalo directamente dentro de `clave.key`.

⚠️ Este archivo es **secreto**. Nunca debe compartirse públicamente.

---

## ⚙️ 3. Configuración de Nginx (`default.conf`)

Ve a la ruta:

```
web/default.conf
```

Y coloca la siguiente configuración:

---

### 🔁 Bloque 1: Redirección HTTP → HTTPS

Este bloque hace que **todas las visitas por HTTP (puerto 80)** se redirijan automáticamente a HTTPS.

```nginx
server {
    listen 80;
    server_name localhost;
    return 301 https://$host$request_uri;
}
```

✅ Así nadie entra sin cifrado.

---

### 🔒 Bloque 2: Servidor seguro HTTPS

Este es el bloque principal de la web segura.

```nginx
server {
    listen 443 ssl;
    server_name localhost;

    root /var/www/html;
    index index.php index.html;

    ssl_certificate /etc/nginx/certs/certificado.crt;
    ssl_certificate_key /etc/nginx/certs/clave.key;

    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers HIGH:!aNULL:!MD5;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        fastcgi_pass php:9000;
        fastcgi_index index.php;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    }
}
```

🧠 Detalles importantes:

- `ssl_certificate` → apunta al certificado
- `ssl_certificate_key` → apunta a la clave privada
- `fastcgi_pass php:9000` → **php es el nombre del servicio en Docker**

---

## 🐳 4. Modificar `docker-compose.yml`

En la **raíz del proyecto**, edita el archivo `docker-compose.yml`:

```yaml
version: "3.9"

services:
  web:
    build: ./web
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./app:/var/www/html:z
      - ./web/default.conf:/etc/nginx/conf.d/default.conf
      - ./web/certs:/etc/nginx/certs
    depends_on:
      - php
    user: root

  php:
    build: ./php
    volumes:
      - ./app:/var/www/html:z
```

📌 Claves de esta configuración:

- Se exponen los puertos **80 y 443**
- Se montan los certificados dentro del contenedor
- Nginx puede acceder a los archivos SSL

---

## 🚀 5. Subir cambios y probar

1. Haz **commit y push** de todos los cambios.
2. Levanta los contenedores.
3. Accede desde el navegador a:

```
https://git.tkdsierranevada.com
```

🔐 Si todo está bien, la web cargará con HTTPS sin advertencias.

---

## 🛠️ 6. Comprobación de errores (si algo falla)

### 🔑 Acceder al servidor por SSH

```bash
ssh -i "clave.pem" ubuntu@IP_ELASTICA
```

---

### 📂 Ir al proyecto

```bash
cd ~/app
```

---

### 👀 Ver estructura de `web`

```bash
ls -R web
```

Sirve para comprobar que `certs`, `default.conf` y los archivos existen.

---

### 📜 Ver logs del contenedor web

```bash
sudo docker-compose logs web
```

Aquí aparecen **errores de Nginx, certificados o puertos**.

---

## ✅ Resumen final

✔ Certificados bien colocados
✔ Nginx configurado para HTTPS
✔ Docker montando certificados correctamente
✔ Redirección automática HTTP → HTTPS

---
