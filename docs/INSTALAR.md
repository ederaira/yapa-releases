# Instalar yapa

> ¿Dudas entre **Lite** y **Servidor**? Mira [EDICIONES.md](EDICIONES.md). ¿Quieres ajustar parámetros? [CONFIGURACION.md](CONFIGURACION.md).
> Antes de nada, ejecuta `./yapa.sh doctor`: revisa tu equipo y te dice qué falta.

yapa se instala con Docker. El asistente `yapa.sh` genera todas las claves, levanta el sistema y deja lista una
**empresa de demostración** (con RUC de pruebas, sedes, series, cajas, productos y ventas de ejemplo), además de tu
propia empresa. Al terminar muestra los usuarios y contraseñas.

Descarga siempre el archivo `yapa-docker-<versión>.zip` de la [última Release](https://github.com/ederaira/yapa-releases/releases/latest).

## Windows 10 / 11

> Estado: se usa con Docker Desktop y WSL2. **Todavía no se ha probado en un equipo Windows real**; si algo falla,
> abre un [issue](https://github.com/ederaira/yapa-releases/issues).

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

## Linux (Ubuntu, Debian, etc.)

```bash
# Docker y openssl instalados (https://docs.docker.com/engine/install/)
unzip yapa-docker-*.zip && cd yapa-docker
./yapa.sh install
```

## macOS

Instala Docker Desktop y ejecuta los mismos comandos de Linux en la Terminal.

## Servidor (VPS) con dominio y HTTPS

Igual que Linux. Cuando el asistente pregunte por el **dominio**, escríbelo (por ejemplo `erp.midominio.pe`): yapa
levanta HTTPS automático con Let's Encrypt. El DNS del dominio debe apuntar ya a la IP del servidor y los puertos
80 y 443 estar abiertos.

Para un VPS nuevo (Ubuntu 24.04 LTS recomendado; sirve amd64 y arm64):

```bash
curl -fsSL https://get.docker.com | sh
apt-get install -y openssl unzip curl
VER=$(curl -s https://api.github.com/repos/ederaira/yapa-releases/releases/latest | grep -oP '"tag_name": "v\K[^"]+')
curl -LO https://github.com/ederaira/yapa-releases/releases/download/v$VER/yapa-docker-$VER.zip
unzip yapa-docker-$VER.zip && cd yapa-docker-$VER
./yapa.sh doctor && ./yapa.sh install
```

Abre solo los puertos **80 y 443** en el firewall del proveedor (el puerto 8080 de la aplicación no debe quedar expuesto).

### Si el inicio de sesión responde 403 con un dominio

Con HTTPS, el navegador envía `https://tu-dominio` y la aplicación puede verlo como otro origen: el login responde
`403` (`Invalid CORS request`). Añade a `.env` `YAPA_CORS_ALLOWED_ORIGINS=https://tu-dominio` y aplica con
`./yapa.sh start`.

> **`restart` no relee el `.env`.** Después de cambiar el `.env` usa `./yapa.sh start` (recrea los contenedores
> cambiados). `./yapa.sh restart` solo reinicia los que ya existen con su configuración anterior.

> **`./yapa.sh update` solo actualiza la imagen**, no `docker-compose.yml`, `Caddyfile` ni `yapa.sh`. Si una versión
> nueva cambia esos archivos (lo dirá en sus notas), reemplázalos con los del `.zip` nuevo.

## Uso diario

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

## Importante

- El archivo **`.env`** que genera el instalador contiene tus contraseñas y la clave que cifra tus secretos
  (`SIGNING_MASTER_KEY`). **Guarda una copia segura**: sin ella no se pueden leer los certificados ni las credenciales
  guardadas. No lo subas a ningún repositorio.
- Los respaldos (`./yapa.sh backup`) incluyen una copia del `.env`: guárdalos en un lugar seguro.
- Requisitos: 2 GB de RAM libres (recomendado 4 GB), 5 GB de disco, Docker con Compose v2 y `openssl`.

## Probar el envío a SUNAT

El asistente puede activar pruebas en el ambiente **beta** de SUNAT mediante FiscalEngine (servicio externo de
facturación electrónica). Si se activa, al final envía una boleta de prueba de la empresa demo y muestra el resultado.
Para emitir comprobantes reales con tu empresa necesitas tu propia cuenta de FiscalEngine con certificado digital y
credenciales SOL.
