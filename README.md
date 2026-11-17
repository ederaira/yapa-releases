# yapa

**Mini ERP gratuito para Pymes de Perú.** Ventas y punto de venta (con Yape y Plin), facturación electrónica SUNAT,
inventario, caja, compras, tesorería, gastos, contabilidad básica, hotelería y restaurante. Se instala en tu propio
equipo o servidor con **un comando** y deja lista una **empresa de demostración** para probar sin configurar nada.

> *La yapa* (del quechua *yapay*, "añadir") es lo que el vendedor peruano regala de más con la compra.

## Instalar en 3 pasos

1. Instala **Docker** ([Docker Desktop](https://www.docker.com/products/docker-desktop/) en Windows y macOS).
2. Descarga `yapa-docker-<versión>.zip` de [**Releases**](../../releases/latest) y descomprímelo.
3. Ejecuta:

```bash
./yapa.sh doctor     # revisa tu equipo (opcional, recomendado)
./yapa.sh install    # instala y muestra tus usuarios y contraseñas
```

Funciona en **Windows (Docker Desktop + WSL2)**, **macOS**, **Linux** y **VPS** (con HTTPS automático si tienes dominio).
Imagen para amd64 y arm64 (también Raspberry Pi / Oracle Ampere).

## Manuales

| Quiero… | Lee |
|---|---|
| Instalarlo (Windows, Linux, macOS, VPS) | [docs/INSTALAR.md](docs/INSTALAR.md) |
| Elegir entre **Lite** (una empresa) y **Servidor** (varias) | [docs/EDICIONES.md](docs/EDICIONES.md) |
| Ver todos los parámetros y recetas listas | [docs/CONFIGURACION.md](docs/CONFIGURACION.md) |

## Qué incluye la demostración

Empresa con RUC de pruebas, 2 sedes con ubigeo, series de factura/boleta/notas, 3 cajas, productos con stock y precios,
plan de cuentas básico, clientes, usuarios (administrador y cajero) y ventas de ejemplo. Con las pruebas de SUNAT
activadas, el instalador envía una boleta al ambiente **beta** de SUNAT (a través de FiscalEngine) y muestra el resultado.

## Soporte y comentarios

Abre un [issue](../../issues) para reportar un problema o sugerir algo. Antes de instalar, `./yapa.sh doctor` te dice qué falta.

## ¿Quieres ofrecer yapa como servicio a tus clientes?

**Puedes.** Sin pedir permiso y sin pagar regalías puedes instalar yapa en **tu propio servidor** y ofrecérselo a tus
clientes como **servicio alojado** (hosting o "yapa en la nube"), cobrando por el servicio, el soporte, la
implementación y la capacitación. Condiciones: usar las versiones oficiales sin modificar, mantener el nombre yapa y la
licencia, y no presentarte como su autor. **No** se permite vender ni redistribuir copias o instaladores. Detalle en la
sección 2 de la [licencia](LICENSE). Para alojar varias empresas en un mismo servidor, mira la
[edición Servidor](docs/EDICIONES.md).

## Licencia

yapa es **gratuito para usar** (incluido uso comercial) y se puede **ofrecer como servicio alojado** (sección anterior), pero **no es de
código abierto**: no se permite modificarlo, descompilarlo ni publicar su código. Ver [LICENSE](LICENSE). Componentes de terceros:
[THIRD-PARTY-NOTICES.md](THIRD-PARTY-NOTICES.md).

Copyright (c) 2026 ederdev.
