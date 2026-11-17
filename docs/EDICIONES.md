# Lite o Servidor: elige tu edición

yapa tiene **dos ediciones del mismo programa**. Se elige al instalar y se puede pasar de una a otra moviendo una copia de seguridad
(ver [Pasar de Lite a Servidor](#pasar-de-lite-a-servidor); esa ruta aún no se ha probado de extremo a extremo).

## ¿Cuál necesito? (30 segundos)

```mermaid
flowchart TD
    A([¿Cuántas empresas vas a administrar?]) -->|Una sola, la mía| B([¿Tienes un técnico o servidor propio?])
    A -->|Varias, o vendo yapa como servicio| S[[Edición SERVIDOR]]
    B -->|No: lo instalo en mi PC o en un VPS sencillo| L[[Edición LITE]]
    B -->|Sí, y quiero Keycloak / MinIO / alta disponibilidad| S
    L --> L2[./yapa.sh install]
    S --> S2[Ver «Instalar la edición Servidor»]
```

**Regla simple:** si es *tu* negocio, usa **Lite**. Si eres un estudio contable, un proveedor de software o necesitas
varias empresas aisladas entre sí, usa **Servidor**.

## Comparación

| | **Lite** | **Servidor** |
|---|---|---|
| Empresas | 1 (más una empresa **demo** opcional) | Ilimitadas, cada una con su propia base de datos |
| Para quién | Una pyme, una tienda, un restaurante, un hotel pequeño | Estudios contables, proveedores del servicio, grupos de empresas |
| Inicio de sesión | Integrado en yapa (sin Keycloak) | Integrado **o** Keycloak (SSO, políticas de contraseña) |
| Archivos (PDF, fotos) | Carpeta en disco | Carpeta en disco **o** MinIO / S3 |
| Módulos | **Todos incluidos**, sin contratación | Contratables por empresa (complementos) |
| Operador de plataforma | No hace falta | Sí: da de alta empresas por API |
| Servicios que necesita | Solo **PostgreSQL** (el instalador lo incluye) | PostgreSQL (+ Keycloak y MinIO si los eliges) |
| Memoria recomendada | 2 GB | 4 GB o más, según empresas |
| Instalación | **Asistente `./yapa.sh install`** | Manual / avanzada (receta abajo) |
| Estado de pruebas | Probada de punta a punta con el instalador | Probada por componentes en desarrollo |

> Honestidad primero: el asistente instala **Lite**. La edición Servidor funciona, pero su instalación todavía es
> manual y su panel de operador no viene empaquetado en la Release (el alta de empresas se hace por API).

## ¿Dónde puedo instalarlo?

| Entorno | ¿Funciona? | Cómo |
|---|---|---|
| **Windows 10/11** | Sí, con Docker Desktop + WSL2 (sin probar en equipo real) | [Guía de Windows](INSTALAR.md#windows-10--11) |
| **macOS** | Sí, con Docker Desktop | `./yapa.sh install` |
| **Linux (Ubuntu, Debian…)** | Sí, probado | `./yapa.sh install` |
| **VPS en la nube** (Oracle, DigitalOcean, Hetzner…) | Sí, con dominio y HTTPS automático | [VPS](INSTALAR.md#servidor-vps-con-dominio-y-https) |
| **Raspberry Pi / ARM** (Oracle Ampere) | Sí: la imagen es multiarquitectura (amd64 + arm64) | `./yapa.sh install` |

**Antes de instalar, deja que yapa revise tu equipo:**

```bash
./yapa.sh doctor
```

Comprueba Docker, memoria, disco, puertos y `openssl`, y te dice exactamente qué falta.

## Instalar la edición Lite

```bash
./yapa.sh install
```

El asistente te pregunta el nombre de tu empresa, el RUC, el correo y la contraseña del administrador, si quieres
HTTPS con dominio propio y si activas las pruebas de SUNAT. Al terminar muestra todos los accesos.
Detalle en [INSTALAR.md](INSTALAR.md).

## Instalar la edición Servidor

Usa el mismo paquete (`yapa-docker-<versión>.zip`) y cambia la configuración **antes del primer arranque**.
Ejecuta `./yapa.sh install` solo si quieres la demo; para Servidor escribe tú el `.env`:

```bash
cd yapa-docker
cat > .env <<'EOF'
SPRING_PROFILES_ACTIVE=server          # <- esto activa la edición Servidor
YAPA_PUBLIC_URL=https://erp.midominio.pe
YAPA_DOMAIN=erp.midominio.pe           # HTTPS automático (opcional)
DB_PASSWORD=<contraseña-larga>
SIGNING_MASTER_KEY=<openssl rand -base64 32>
YAPA_STORAGE_SIGNING_SECRET=<openssl rand -base64 32>
YAPA_INTERNAL_SECRET=<openssl rand -base64 32>
YAPA_AUTH_PROVIDER=builtin             # o keycloak (ver CONFIGURACION.md)
YAPA_OPERATOR_USERNAME=operador        # operador de plataforma
YAPA_OPERATOR_PASSWORD=<contraseña-larga>
EOF
docker compose --profile https up -d
```

Después, el operador da de alta cada empresa:

```bash
TOKEN=$(curl -s -X POST https://erp.midominio.pe/realms/master/protocol/openid-connect/token \
  -d grant_type=password -d client_id=admin-cli -d username=operador --data-urlencode "password=$YAPA_OPERATOR_PASSWORD" \
  | grep -o '"access_token":"[^"]*"' | cut -d'"' -f4)
curl -X POST https://erp.midominio.pe/api/v1/tenants -H "Authorization: Bearer $TOKEN" -H 'Content-Type: application/json' \
  -d '{"slug":"acme","registrationName":"Acme SAC","companyId":"20123456789","countryCode":"PE","fiscalAdapter":"SUNAT","dbHost":"postgres","dbPort":5432}'
```

Al primer arranque yapa crea una empresa técnica llamada `bootstrap` (puedes ignorarla). Cada empresa que des de alta recibe su propia base de datos. Sus usuarios entran con el slug de su empresa en el login.
Para Keycloak, MinIO y todos los demás parámetros, consulta **[CONFIGURACION.md](CONFIGURACION.md)**.

## Pasar de Lite a Servidor

Tus datos viajan con una copia de seguridad: no se pierde nada.

```bash
./yapa.sh backup                       # en la instalación Lite
# instala la edición Servidor y copia la carpeta backups/<fecha>
./yapa.sh restore backups/<fecha>      # en el Servidor
```

Usa el **mismo `SIGNING_MASTER_KEY`** (viene en `env.backup` dentro del respaldo): sin él no se pueden leer los
certificados ni las credenciales cifradas.
