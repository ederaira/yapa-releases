# Configuración: todos los parámetros

yapa se configura **solo con variables de entorno**, que van en el archivo `.env` (el asistente lo genera con claves
seguras). Un valor vacío significa «no configurado».

> **Secretos:** `DB_PASSWORD`, `SIGNING_MASTER_KEY`, `YAPA_*_SECRET` y las contraseñas son privados. No los subas a ningún
> repositorio. `chmod 600 .env`.

## 1. Lo esencial (el asistente ya lo rellena)

| Variable | Qué hace | Por defecto |
|---|---|---|
| `SPRING_PROFILES_ACTIVE` | `lite` = edición Lite; `server` = edición Servidor | `lite` (en el paquete Docker) |
| `YAPA_PUBLIC_URL` | Dirección pública con la que se accede (p. ej. `https://erp.midominio.pe`) | `http://localhost:8080` |
| `YAPA_HTTP_PORT` | Puerto en tu equipo | `8080` |
| `YAPA_DOMAIN` | Dominio para HTTPS automático (Let's Encrypt). Vacío = sin HTTPS | — |
| `DB_PASSWORD` | Contraseña de PostgreSQL | generada |
| `SIGNING_MASTER_KEY` | Clave (base64, 32 bytes) que **cifra** certificados, claves SOL y credenciales. **Si la pierdes no se pueden leer** | generada |
| `YAPA_STORAGE_SIGNING_SECRET` | Firma los enlaces de descarga de archivos | generada |
| `YAPA_INTERNAL_SECRET` | Secreto de las llamadas internas. Vacío = se genera uno al arrancar | generada |

## 2. Edición Lite: tu empresa

| Variable | Qué hace | Por defecto |
|---|---|---|
| `YAPA_COMPANY_NAME` | Razón social | `Mi empresa` |
| `YAPA_COMPANY_SLUG` | Identificador corto (minúsculas, números y guiones). Es lo que se escribe como «Empresa» al iniciar sesión | `empresa` |
| `YAPA_COMPANY_ID` | RUC | `00000000000` |
| `YAPA_ADMIN_EMAIL` | Correo del administrador inicial | — |
| `YAPA_ADMIN_PASSWORD` | Contraseña del administrador inicial (mín. 8) | — |
| `YAPA_DEMO_ENABLED` | Crea la empresa de demostración `demo` junto a la tuya | `false` (el asistente la activa) |

Estas variables solo se usan en el **primer arranque**; después los cambios se hacen desde la aplicación.

## 3. Base de datos

| Variable | Qué hace | Por defecto |
|---|---|---|
| `CONTROL_DB_URL` / `_USER` / `_PASS` | Base de control (empresas, usuarios, planes) | `jdbc:postgresql://localhost:5433/yapa_control` |
| `NOTIFICATION_DB_URL` / `_USER` / `_PASS` | Base de notificaciones | `…/yapa_notifications` |
| `TENANT_DB_USER` / `TENANT_DB_PASS` | Usuario con el que cada empresa usa su base | — |
| `TENANT_DB_ADMIN_USER` / `_PASSWORD` | Usuario con permiso para **crear** bases de empresas nuevas | — |
| `TENANT_DB_BOOTSTRAP_HOST` / `_PORT` | Dónde se crean las bases de las empresas | `pg-tenants` / `5432` |
| `TENANT_DB_POOL_MAX_SIZE` | Conexiones por empresa (con más de ~50 empresas usa PgBouncer) | `3` |
| `TENANT_DB_POOL_IDLE_MS` | Tiempo antes de cerrar una conexión inactiva | `60000` |
| `TENANT_DB_MIGRATION_PARALLELISM` | Migraciones simultáneas de empresas al arrancar | `4` |

Requiere **PostgreSQL 16**. Cada empresa tiene su propia base (`yapa_tenant_<slug>`).

## 4. Inicio de sesión (identidad)

| Variable | Qué hace | Por defecto |
|---|---|---|
| `YAPA_AUTH_PROVIDER` | `builtin` = integrado en yapa · `keycloak` = Keycloak externo | `builtin` (Lite) / `keycloak` (sin perfil) |
| `YAPA_OPERATOR_USERNAME` / `_PASSWORD` | Operador de plataforma inicial (solo `builtin`) | — |
| `YAPA_SECURITY_MODE` | `jwt` = login real. `open` = **sin autenticación, solo para desarrollo** | `jwt` |
| `KEYCLOAK_SERVER_URL` | Dirección de Keycloak (solo `keycloak`) | `http://localhost:8180` |
| `KEYCLOAK_ISSUER_URI` | Emisor del realm `master`: `<keycloak>/realms/master` | `http://localhost:8180/realms/master` |
| `KEYCLOAK_TENANT_REALM` | Realm único de los usuarios de las empresas | `yapa` |
| `KEYCLOAK_ADMIN_USERNAME` / `_PASSWORD` / `_REALM` / `_CLIENT_ID` | Cuenta administradora de Keycloak que usa yapa para crear usuarios | `admin` / `admin` / `master` / `admin-cli` |

Con `builtin`, yapa emite sus propios tokens y atiende `/realms/{realm}/protocol/openid-connect/*` (compatible con
Keycloak). Un usuario se bloquea 15 minutos tras 5 intentos fallidos.

## 5. Archivos (PDF, XML, fotos, documentos)

| Variable | Qué hace | Por defecto |
|---|---|---|
| `YAPA_STORAGE_PROVIDER` | `filesystem` = carpeta en disco · `s3` = MinIO o cualquier S3 | `filesystem` |
| `YAPA_STORAGE_PATH` | Carpeta (con `filesystem`) | `./data/files` (`/data/files` en Docker) |
| `MINIO_ENDPOINT` | Dirección S3 (con `s3`) | `http://localhost:9000` |
| `MINIO_ACCESS_KEY` / `MINIO_SECRET_KEY` | Credenciales S3 | `minioadmin` |
| `MINIO_BUCKET` / `MINIO_BUCKET_DOCUMENTS` | Buckets de comprobantes y de documentos | `yapa-invoices` / `yapa-documents` |
| `MINIO_PRESIGNED_EXPIRY_MINUTES` | Vigencia de los enlaces de descarga | `60` |

## 6. Panel web y red

| Variable | Qué hace | Por defecto |
|---|---|---|
| `YAPA_WEB_DIR` | Carpeta del panel web compilado. La imagen Docker ya lo trae (`/app/web`). Vacío = solo API | — |
| `YAPA_CORS_ALLOWED_ORIGINS` | Orígenes (separados por coma) desde los que se abre un panel: con dominio y HTTPS, el principal **y** el del operador (`https://erp.midominio.pe,https://admin.midominio.pe`). Si falta, el login responde 403 `Invalid CORS request`. Vacío = solo mismo origen | — |
| `SELF_URL` | Dirección con la que yapa se llama a sí misma | `http://localhost:8080` |
| `YAPA_PLATFORM_WEB_DIR` | Carpeta del panel del operador compilado. La imagen Docker ya lo trae (`/app/platform-web`) y lo sirve bajo `/_platform/` | — |

### Panel del operador en un dominio propio (edición Servidor)

Lo activa `./yapa.sh admin-panel` (requiere `YAPA_DOMAIN` y que el DNS del subdominio ya apunte al servidor). Estas
variables viven en el `.env`; el comando las escribe por ti y puedes cambiarlas después:

| Variable | Qué hace | Por defecto |
|---|---|---|
| `YAPA_ADMIN_DOMAIN` | Dominio del panel del operador, por ejemplo `admin.midominio.pe`. Sin este dominio no se publica | — |
| `YAPA_ADMIN_BASIC_USER` | Usuario de la Basic Auth que protege el panel | `admin` |
| `YAPA_ADMIN_BASIC_HASH` | Hash bcrypt de la clave de Basic Auth (se genera con `./yapa.sh admin-panel`) | — |

El hash lleva `$`: escríbelo **entre comillas simples** en el `.env`. Basic Auth protege solo la página del panel; las
llamadas a la API llevan su propio token y no pasan por ella. Después de Basic Auth, el operador inicia sesión con
`YAPA_OPERATOR_USERNAME` / `YAPA_OPERATOR_PASSWORD`.

## 7. Correo (envío de comprobantes por email)

| Variable | Qué hace | Por defecto |
|---|---|---|
| `MAIL_HOST` / `MAIL_PORT` | Servidor SMTP | `smtp.gmail.com` / `587` |
| `MAIL_USERNAME` / `MAIL_PASSWORD` | Credenciales SMTP | — |

## 8. Facturación electrónica SUNAT (FiscalEngine)

yapa no firma comprobantes: los envía a **FiscalEngine** (servicio externo), que los firma y transmite a SUNAT.

| Variable | Qué hace | Por defecto |
|---|---|---|
| `FISCALENGINE_BASE_URL` | Dirección de la API de FiscalEngine | — |
| `FISCALENGINE_RESELLER_API_KEY` | Llave de *reseller*: da de alta tu empresa automáticamente en FiscalEngine | — |
| `FISCALENGINE_WEBHOOK_BASE_URL` | Dirección pública de tu yapa para que FiscalEngine avise los resultados. Vacío = yapa consulta cada 2 min | — |
| `FISCALENGINE_DEMO_TENANT_KEY` | (solo instalador) llave del tenant BETA que usa la empresa demo | — |

La llave de cada empresa también se puede cargar desde **Integraciones externas** (proveedor `FISCALENGINE`, clave
`api_key`). Para producción cada empresa necesita su certificado digital y credenciales SOL cargados en FiscalEngine.

## 9. Opcionales

| Variable | Qué hace |
|---|---|
| `ALERT_TELEGRAM_BOT_TOKEN` / `ALERT_TELEGRAM_CHAT_ID` | Avisos de salud del sistema por Telegram |
| `FCM_PROJECT_ID` / `FCM_SERVICE_ACCOUNT_KEY_PATH` | Notificaciones push a las apps móviles (Firebase) |
| `STRIPE_WEBHOOK_SECRET` | Cobro de suscripciones con Stripe (edición Servidor) |
| `INVOICE_SHARE_LINK_SECRET` | Firma los enlaces públicos de comprobantes. Vacío = se genera uno por arranque (los enlaces caducan al reiniciar) |
| `APIPERU_BASE_URL` | Servicio de consulta de RUC/DNI (la llave se carga en Integraciones externas) |
| `PAYMENTS_SIMULATION_ENABLED` | Simula Yape/Plin **solo para desarrollo** |
| `FISCAL_DEFAULT_COUNTRY` | País fiscal por defecto | `PE` |
| `JAVA_OPTS` | Opciones de la JVM (memoria, etc.). Por defecto `-XX:MaxRAMPercentage=65 -XX:+UseSerialGC` |
| `TZ` | Zona horaria (el paquete usa `America/Lima`) |

## Recetas listas para copiar

### A. Lite en tu PC o VPS (lo que hace el asistente)

```
SPRING_PROFILES_ACTIVE=lite
YAPA_PUBLIC_URL=http://localhost:8080
YAPA_COMPANY_NAME='Mi Tienda SAC'
YAPA_COMPANY_SLUG=mitienda
YAPA_COMPANY_ID=20123456789
YAPA_ADMIN_EMAIL=dueno@mitienda.pe
YAPA_ADMIN_PASSWORD='ClaveSegura123!'
DB_PASSWORD=...  SIGNING_MASTER_KEY=...  YAPA_STORAGE_SIGNING_SECRET=...  YAPA_INTERNAL_SECRET=...
```

### B. Lite con HTTPS en un VPS

Igual que A, más `YAPA_DOMAIN=erp.midominio.pe` y `YAPA_PUBLIC_URL=https://erp.midominio.pe`, y arranca con
`docker compose --profile https up -d`. El DNS debe apuntar al servidor y los puertos 80/443 estar abiertos.

### C. Servidor multiempresa con login integrado

```
SPRING_PROFILES_ACTIVE=server
YAPA_AUTH_PROVIDER=builtin
YAPA_OPERATOR_USERNAME=operador
YAPA_OPERATOR_PASSWORD='...'
```

### D. Servidor con Keycloak y MinIO

```
SPRING_PROFILES_ACTIVE=server
YAPA_AUTH_PROVIDER=keycloak
KEYCLOAK_SERVER_URL=https://login.midominio.pe
KEYCLOAK_ISSUER_URI=https://login.midominio.pe/realms/master
KEYCLOAK_ADMIN_USERNAME=admin  KEYCLOAK_ADMIN_PASSWORD='...'
YAPA_STORAGE_PROVIDER=s3
MINIO_ENDPOINT=https://minio.midominio.pe  MINIO_ACCESS_KEY=...  MINIO_SECRET_KEY=...
```

El `docker-compose.yml` del paquete no incluye Keycloak ni MinIO: levántalos aparte y agrega las variables que uses a la sección `environment` del servicio `app`.
