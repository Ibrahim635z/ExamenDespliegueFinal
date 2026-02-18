# 🌐 VISIÓN GENERAL (la idea madre)

Todo gira alrededor de **cómo funciona una web por dentro**:

1. Alguien escribe una dirección en el navegador
2. Internet sabe **a qué servidor ir**
3. El servidor **envía la web**
4. Si la web es “lista”, **ejecuta código y usa datos**
5. Todo debe ser **rápido, estable y seguro**

---

## 🟡 RA1 — Conocer e instalar servidores web y aplicaciones

👉 **Objetivo real:** entender _qué piezas existen_ y _para qué sirve cada una_.

---

### a) Arquitecturas web

**Idea clave:**
Una web **no es una sola cosa**, son varias piezas trabajando juntas.

Las 3 piezas básicas:

- 🧭 **Navegador** → donde tú ves la web
- 🖥️ **Servidor** → donde vive la web
- 🗄️ **Base de datos** → donde se guardan datos

Modelo mental:

> El navegador pide → el servidor piensa → la base de datos recuerda

Ventajas:

- Acceso desde cualquier sitio
- No necesitas instalar nada
  Inconvenientes:
- Dependes de Internet
- Hay riesgos de seguridad

📌 **Si entiendes esto, entiendes TODA la web moderna.**

---

### b) Cómo funciona un servidor web

**Idea clave:**
Navegador y servidor **hablan con normas**.

Esas normas son:

- **HTTP** → conversación normal
- **HTTPS** → conversación cifrada (segura 🔐)

Ejemplo mental:

> HTTP = hablar en voz alta
> HTTPS = hablar en secreto

---

### c) Instalar un servidor web

**Idea clave:**
Un servidor web es un programa que:

- Escucha peticiones
- Envía archivos (HTML, imágenes…)

Ejemplos:

- Apache
- Nginx

Lo importante **no es memorizar comandos**, sino saber que:

- Hay que instalarlo
- Arrancarlo
- Comprobar que muestra una página

---

### d) Servidores de aplicaciones

**Idea clave:**
Un servidor web **solo enseña cosas**.
Un servidor de aplicaciones **hace cosas**.

Puede:

- Ejecutar código
- Conectar con bases de datos
- Tomar decisiones

Ejemplos:

- Tomcat (Java)
- WildFly

Modelo mental:

> Web server = escaparate
> App server = cerebro

---

### e) Instalar un servidor de aplicaciones

**Idea clave:**
Es igual que antes, pero para aplicaciones “inteligentes”.

Lo importante:

- Saber que existen
- Saber que se instalan aparte
- Saber que ejecutan lógica

---

### f) Probar que todo funciona

**Idea clave:**
Nada vale si:

- No carga
- Da errores
- Responde mal

Siempre hay que **probar**, no asumir.

---

### g) Partes de una aplicación web

**Idea clave:**
Una web tiene orden interno.

Suele haber:

- Archivos visibles (HTML, imágenes)
- Código (PHP, Java…)
- Configuración
- Datos

📌 Entender esto evita romper cosas.

---

### h) Implantar una aplicación web

**Idea clave:**
Subir una web a Internet **no es solo copiar archivos**.

Hace falta:

- Servidor
- Configuración
- Seguridad mínima

---

### i) Documentar

**Idea clave (muy examen):**
Si no está escrito → **no existe**.

Documentar es:

- Explicar qué hiciste
- Para que otros (o tú) lo entiendan

---

## 🔵 RA2 — Configurar servidores web de forma segura

👉 **Objetivo real:** que la web no sea un coladero.

---

### a) Parámetros importantes

**Idea clave:**
Un servidor tiene ajustes críticos:

- Puertos (por dónde entra la gente)
- Carpetas (qué se puede ver)
- Usuarios (quién manda)

---

### b) Activar módulos

**Idea clave:**
Los servidores son modulares:

- Puedes añadir funciones
- O quitarlas

Ejemplos:

- HTTPS
- Redirecciones
- Seguridad

---

### c) Sitios virtuales _(no dado)_

**Idea clave:**
Un solo servidor → muchas webs.

Como un edificio con varios pisos.

---

### d) Usuarios y contraseñas _(no dado)_

**Idea clave:**
No todo el mundo puede hacer todo.

---

### e) Certificados digitales

**Idea clave CLAVÍSIMA:**
El candado 🔒 significa:

- Datos cifrados
- Confianza
- Identidad verificada

---

### f) Comunicación segura

**Idea clave:**
Que nadie pueda:

- Espiar
- Modificar datos

---

### g) Probar rendimiento

**Idea clave:**
No basta con que funcione:

- Tiene que aguantar gente
- Tiene que ir rápido

---

### h) Ajustes para aplicaciones

**Idea clave:**
Cada app necesita:

- Un entorno adecuado
- Recursos suficientes

---

### i) Documentación

**Idea clave:**
Explicar:

- Qué ajustes hay
- Por qué son seguros

---

## 🟣 RA3 — Servidores de aplicaciones

👉 **Objetivo real:** entender el “backend”.

---

### a) Qué hace un servidor de aplicaciones

**Idea clave:**
Gestiona:

- Código
- Usuarios
- Datos

Es el motor real de la app.

---

### b) Archivos importantes

**Idea clave:**
No todo se toca.

Hay archivos:

- Críticos
- De configuración
- De despliegue

---

### c) Web + aplicaciones juntos

**Idea clave:**
Trabajan en equipo:

- Web server → muestra
- App server → piensa

---

### d) Seguridad

**Idea clave:**
Proteger accesos:

- Usuarios
- Permisos
- Contraseñas

---

### e) Componentes web

**Idea clave:**
Cada tecnología tiene piezas propias
(en Java: Servlets, JSP).

---

### f) Preparar el despliegue

**Idea clave:**
Antes de subir:

- Revisar
- Ajustar
- Evitar errores

---

### g) Probar la aplicación

**Idea clave:**
Probar ≠ abrir una vez
Probar = comprobar que resiste.

---

### h) Documentar servidor

**Idea clave:**
Cómo se administra.

---

### i) Documentar despliegue

**Idea clave:**
Cómo se subió paso a paso.

---

## 🟢 RA5 — Servicios de red y funcionamiento correcto

👉 **Objetivo real:** que Internet **sepa encontrarte** y **reconocerte**.

---

### a) DNS

**Idea clave FUNDAMENTAL:**
DNS = traductor de nombres.

Convierte:

- google.com → IP

Sin DNS, Internet no funciona.

---

### b) Configurar DNS

**Idea clave:**
Decidir:

> “Este nombre va a este servidor”

---

### c) Servicio de directorio

**Idea clave:**
Base de datos de identidades.

Guarda:

- Usuarios
- Grupos
- Permisos

---

### d) Configurar directorio _(no dado)_

**Idea clave:**
Personalizar cómo se gestionan usuarios.

---

### e) Autenticación centralizada _(no dado)_

**Idea clave MUY IMPORTANTE:**
Un solo login para todo.

---

### f) Validación de usuarios _(no dado)_

**Idea clave:**
Cómo la web comprueba quién eres.

---

### g) Documentación

**Idea clave final:**
Registrar cambios = control y orden.

---

## 💛 RESUMEN FINAL PARA EXAMEN

Si recuerdas esto, vas sobrada:

- Una web son **piezas que cooperan**
- DNS = nombres
- Servidor web = muestra
- Servidor de aplicaciones = piensa
- Seguridad = cifrar + limitar
- Documentar = demostrar que sabes
