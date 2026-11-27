# Instalar yapa

> Antes de nada, ejecuta `./yapa.sh doctor`: revisa tu equipo y te dice qué falta.
> ¿Quieres ajustar parámetros? [CONFIGURACION.md](CONFIGURACION.md).

## 1. Elige la edición

| | **Lite** (por defecto) | **Servidor** |
|---|---|---|
| Para quién | Una pyme, una tienda, un restaurante, un hotel pequeño | Estudios contables, quien ofrece yapa como servicio, grupos de empresas |
| Empresas | **Una**, más una empresa de demostración | **Varias**, cada una con su propia base de datos |
| Quién da de alta empresas | No hace falta | El **operador de plataforma**, desde un panel propio |
| Cómo se instala | `./yapa.sh install` | `YAPA_EDITION=server ./yapa.sh install` |

> **Si no indicas nada, se instala Lite.** Para instalar la edición **Servidor** debes poner `YAPA_EDITION=server`
> delante del comando, como se ve en la tabla. Más detalle en [EDICIONES.md](EDICIONES.md).

## 2. Qué necesitas

- Docker con Compose v2 y `openssl` (el instalador te avisa si falta algo).
- 2 GB de RAM libres (recomendado 4 GB) y 5 GB de disco.
- Para usar **HTTPS con un dominio propio**: el DNS del dominio ya apuntando a la IP del servidor y los puertos
  **80 y 443** abiertos. En la edición Servidor, también el DNS del subdominio del operador (por ejemplo
  `admin.midominio.pe`).
- Descarga siempre `yapa-docker-<versión>.zip` de la [última Release](https://github.com/ederaira/yapa-releases/releases/latest).

## 3. Instalar

Descomprime el `.zip`, entra a la carpeta y ejecuta **uno** de los dos comandos:

```bash
unzip yapa-docker-*.zip && cd yapa-docker
./yapa.sh doctor                          # revisa tu equipo (recomendado)

./yapa.sh install                         # LITE: una empresa + demostración
YAPA_EDITION=server ./yapa.sh install     # SERVIDOR: varias empresas, sin demostración
```

### Qué pregunta y qué entrega cada una

**Lite** pregunta el nombre y el RUC de tu empresa, el correo y la contraseña del administrador, si activas las pruebas
de SUNAT y el dominio para HTTPS (vacío = solo en este equipo, puerto 8080). Al terminar muestra los usuarios y
contraseñas de **tu empresa** y de la **empresa de demostración** (RUC de pruebas, sedes, series, cajas, productos y
ventas de ejemplo).

**Servidor** pregunta solo el dominio principal (vacío = sin HTTPS, para pruebas) y el del panel del operador
(por defecto `admin.<dominio>`). Pide además el usuario y la clave de Basic Auth que protegerá ese panel (o genera una).
No crea ninguna empresa: al terminar muestra el usuario y la clave del **operador de plataforma**, que da de alta las
empresas desde el panel. Detalle en [EDICIONES.md](EDICIONES.md).

En ambos casos los accesos quedan guardados en el archivo `.env`; para verlos de nuevo: `./yapa.sh credentials`.

## 4. Según tu sistema

### Servidor VPS nuevo (Ubuntu 24.04 LTS recomendado; amd64 o arm64)

Como `root`, instala lo necesario, descarga la última versión y ejecuta el instalador de la edición que quieras:

```bash
curl -fsSL https://get.docker.com | sh
apt-get install -y openssl unzip curl
VER=$(curl -s https://api.github.com/repos/ederaira/yapa-releases/releases/latest | grep -oP '"tag_name": "v\K[^"]+')
curl -LO https://github.com/ederaira/yapa-releases/releases/download/v$VER/yapa-docker-$VER.zip
unzip yapa-docker-$VER.zip && cd yapa-docker
chmod +x yapa.sh seed-demo.sh
./yapa.sh doctor

./yapa.sh install                         # Lite
# o:
YAPA_EDITION=server ./yapa.sh install     # Servidor
```

Abre en el firewall del proveedor solo los puertos **80 y 443**. El puerto 8080 de la aplicación no debe quedar
expuesto a internet.

### Linux (Ubuntu, Debian, etc.)

Docker y openssl instalados ([guía oficial](https://docs.docker.com/engine/install/)) y los comandos del apartado 3.

### macOS

Instala [Docker Desktop](https://www.docker.com/products/docker-desktop/) y ejecuta los comandos del apartado 3 en la
Terminal.

### Windows 10 / 11

> Estado: se usa con Docker Desktop y WSL2. **Todavía no se ha probado en un equipo Windows real**; si algo falla,
> abre un [issue](https://github.com/ederaira/yapa-releases/issues). Se recomienda **Lite**; la edición Servidor es
> para un servidor Linux.

1. Instala [Docker Desktop](https://www.docker.com/products/docker-desktop/). Deja marcada la opción **WSL 2**.
2. Instala Ubuntu desde la Microsoft Store (o en PowerShell: `wsl --install -d Ubuntu`) y ábrelo.
3. En Docker Desktop → *Settings → Resources → WSL Integration*, activa **Ubuntu**.
4. Descarga el `.zip` de la Release, descomprímelo y, **en la terminal de Ubuntu**, entra a la carpeta
   (tus archivos de Windows están en `/mnt/c/Users/<tu-usuario>/Downloads/...`):

   ```bash
   cd /mnt/c/Users/<tu-usuario>/Downloads/yapa-docker
   chmod +x yapa.sh seed-demo.sh
   ./yapa.sh install
   ```

   Para que sea más rápido y estable, copia antes la carpeta al disco de Linux: `cp -r yapa-docker ~ && cd ~/yapa-docker`.
5. Abre <http://localhost:8080> en el navegador.

No ejecutes `yapa.sh` desde PowerShell o CMD: es un script de bash y debe correr en la terminal de Ubuntu (WSL).

## 5. Después de instalar

- **Lite:** entra a la dirección que muestra el instalador (`https://tu-dominio` o `http://localhost:8080`) con el
  usuario de tu empresa.
- **Servidor:** entra a `https://admin.tu-dominio`. El navegador pide primero el usuario y la clave de **Basic Auth** y
  luego el login del **operador** (`operador` y la clave que mostró el instalador). Desde ahí das de alta cada empresa;
  sus usuarios inician sesión con el nombre corto (slug) de su empresa.
  Si no publicaste el panel durante la instalación, hazlo después con `./yapa.sh admin-panel`.

## 6. Uso diario

```bash
./yapa.sh status                 # estado
./yapa.sh logs                   # registros
./yapa.sh credentials            # ver de nuevo usuarios y contraseñas
./yapa.sh backup                 # respaldo de la base de datos y los archivos
./yapa.sh restore backups/<carpeta>
./yapa.sh update                 # actualizar a la última versión
./yapa.sh admin-panel            # (Servidor) publicar el panel del operador en admin.<tu-dominio>
./yapa.sh stop | start | restart
```

## 7. Problemas frecuentes

**El inicio de sesión responde 403 con un dominio (`Invalid CORS request`).** Con HTTPS, el navegador envía
`https://tu-dominio` y la aplicación puede verlo como otro origen (cuerpo de 20 bytes en la pestaña *Red*). Pasa en cada
dominio desde el que abras un panel, incluido el del operador. Los instaladores recientes y `./yapa.sh admin-panel` ya
lo escriben solos; si instalaste con una versión anterior, lista **todos** los orígenes en el `.env`, separados por
coma, y aplica con `./yapa.sh start`:

```bash
YAPA_CORS_ALLOWED_ORIGINS=https://erp.midominio.pe,https://admin.midominio.pe
```

**Cambié el `.env` y no se nota.** `./yapa.sh restart` **no** relee el `.env`. Después de cambiarlo usa
`./yapa.sh start`, que recrea los contenedores cambiados.

**`./yapa.sh update` no trae algo que cambió en la versión.** `update` solo actualiza la imagen, no
`docker-compose.yml`, `Caddyfile` ni `yapa.sh`. Si una versión cambia esos archivos (lo dirá en sus notas),
reemplázalos con los del `.zip` nuevo y luego ejecuta `./yapa.sh update`:

```bash
unzip -o -j yapa-docker-<versión>.zip 'yapa-docker/docker-compose.yml' 'yapa-docker/Caddyfile' 'yapa-docker/yapa.sh' 'yapa-docker/seed-demo.sh' -d .
chmod +x yapa.sh seed-demo.sh && ./yapa.sh update
```

**Quiero pasar de Lite a Servidor.** No cambies el `.env` a mano: usa el respaldo, ver
[EDICIONES.md](EDICIONES.md#pasar-de-lite-a-servidor).

## 8. Importante

- El archivo **`.env`** que genera el instalador contiene tus contraseñas y la clave que cifra tus secretos
  (`SIGNING_MASTER_KEY`). **Guarda una copia segura**: sin ella no se pueden leer los certificados ni las credenciales
  guardadas. No lo subas a ningún repositorio.
- Los respaldos (`./yapa.sh backup`) incluyen una copia del `.env`: guárdalos en un lugar seguro.

## Probar el envío a SUNAT

El asistente de **Lite** puede activar pruebas en el ambiente **beta** de SUNAT mediante FiscalEngine (servicio externo de
facturación electrónica). Si se activa, al final envía una boleta de prueba de la empresa demo y muestra el resultado.
Para emitir comprobantes reales con tu empresa necesitas tu propia cuenta de FiscalEngine con certificado digital y
credenciales SOL.
