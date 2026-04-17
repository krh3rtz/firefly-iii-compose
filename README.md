# Firefly III - Docker Compose Setup

[![Docker Hub](https://img.shields.io/badge/docker-fireflyiii%2Fcore-blue.svg)](https://hub.docker.com/r/fireflyiii/core)
[![License: AGPL-3.0](https://img.shields.io/badge/License-AGPL--3.0-blue.svg)](https://github.com/firefly-iii/firefly-iii/blob/main/LICENSE)

Este repositorio contiene una configuración optimizada de **Docker Compose** para desplegar [Firefly III](https://www.firefly-iii.org/), un gestor de finanzas personales de código abierto diseñado para ayudarte a rastrear tus gastos, ingresos y presupuestos.

## 🚀 Características de esta configuración

- **Firefly III Core**: La última versión estable de la aplicación.
- **MariaDB**: Base de datos robusta y persistente.
- **Cron Job Integrado**: Contenedor Alpine dedicado para automatizar tareas recurrentes y notificaciones.
- **Persistencia de Datos**: Volúmenes Docker configurados para proteger tus datos y archivos subidos.
- **Red Aislada**: Comunicación segura entre contenedores mediante una red interna.

---

## 🛠️ Requisitos Previos

Antes de comenzar, asegúrate de tener instalados:
- [Docker Engine](https://docs.docker.com/get-docker/) (v20.10+)
- [Docker Compose](https://docs.docker.com/compose/install/) (v2.0+)

---

## 📦 Instalación Paso a Paso

### 1. Preparar el directorio
Asegúrate de estar en el directorio donde se encuentra el archivo `docker-compose.yaml`:
```bash
cd path/to/firefly-iii-compose
```

### 2. Configurar variables de entorno
Firefly III requiere dos archivos de configuración.

**A. Base de Datos:**
Copia el archivo de ejemplo y edita las credenciales:
```bash
cp .db.env.example .db.env
```
Edita `.db.env` y define una contraseña segura para `MYSQL_PASSWORD`.

**B. Aplicación (Core):**
Crea un archivo llamado `.env` basado en la [plantilla oficial de Firefly III](https://raw.githubusercontent.com/firefly-iii/firefly-iii/main/.env.example).
> **Importante:** Debes generar una `APP_KEY` de 32 caracteres exactos. Puedes usar el siguiente comando para generar una:
> `head /dev/urandom | tr -dc A-Za-z0-9 | head -c 32 ; echo`

### 3. Configurar el Cron (Opcional pero recomendado)
Para que las transacciones recurrentes funcionen, edita el archivo `docker-compose.yaml`. En la sección `cron`, asegúrate de que el comando incluya tu token de cron configurado en el `.env`:
```yaml
command: sh -c "echo \"0 3 * * * wget -qO- http://app:8080/api/v1/cron/TU_STATIC_CRON_TOKEN_AQUI\";echo\" | crontab - && crond -f -L /dev/stdout"
```

### 4. Desplegar
Levanta los contenedores en segundo plano:
```bash
docker-compose up -d
```

---

## 🖥️ Acceso y Uso

Una vez que los contenedores estén en ejecución, abre tu navegador y accede a:
👉 **[http://localhost:8888](http://localhost:8888)**

La primera vez que accedas, se te pedirá registrar una cuenta de usuario (esta será la cuenta de administrador local).

---

## 📂 Estructura de Volúmenes

Para evitar la pérdida de datos, se utilizan los siguientes volúmenes:
- `firefly_iii_upload`: Almacena documentos, facturas y archivos adjuntos.
- `firefly_iii_db`: Almacena todas las tablas de la base de datos MariaDB.

---

## ⚙️ Comandos Útiles

**Ver logs en tiempo real:**
```bash
docker-compose logs -f app
```

**Detener los servicios:**
```bash
docker-compose stop
```

**Actualizar Firefly III:**
```bash
docker-compose pull
docker-compose up -d
```

---

## 🛡️ Notas de Seguridad

1. **Contraseñas**: Nunca uses las contraseñas por defecto en un entorno real.
2. **Backups**: Se recomienda realizar copias de seguridad periódicas del volumen `firefly_iii_db`.
3. **HTTPS**: Si planeas exponer esto a internet, utiliza un proxy inverso como Nginx Proxy Manager o Traefik con certificados SSL.

---

## 📄 Licencia
Este proyecto de configuración se distribuye bajo la licencia MIT. Firefly III es un software bajo licencia AGPL-3.0.