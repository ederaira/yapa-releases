# Avisos de componentes de terceros

yapa incluye software de terceros. Cada componente se rige por **su propia licencia**, que prevalece sobre la licencia
de uso de yapa para ese componente. La lista completa de componentes del servidor está en
[`third-party-components.txt`](third-party-components.txt) (un artefacto por línea, `grupo:nombre:versión`).

## Familias principales

| Componente | Uso en yapa | Licencia |
|---|---|---|
| Spring Boot, Spring Framework, Spring Security, Spring Data, Spring Cloud OpenFeign | Base del servidor | Apache-2.0 |
| Hibernate ORM | Acceso a datos | Apache-2.0 (versión 6.x del Software) |
| Flyway (Community) | Migraciones de base de datos | Apache-2.0 |
| PostgreSQL JDBC Driver | Conexión a PostgreSQL | BSD-2-Clause |
| Jackson | JSON | Apache-2.0 |
| Nimbus JOSE + JWT | Tokens de sesión | Apache-2.0 |
| Keycloak admin client | Integración opcional con Keycloak | Apache-2.0 |
| MinIO Java SDK | Almacenamiento S3 opcional | Apache-2.0 |
| Caffeine | Caché en memoria | Apache-2.0 |
| Thymeleaf | Plantillas de comprobantes | Apache-2.0 |
| Apache PDFBox | Generación de PDF | Apache-2.0 |
| **OpenHTMLtoPDF** | Generación de PDF de comprobantes | **LGPL-3.0** (se usa sin modificar, como biblioteca; puedes reemplazarla por otra versión compatible) |
| ZXing | Códigos QR | Apache-2.0 |
| Bouncy Castle | Criptografía | MIT |
| Logback / SLF4J | Registros | EPL-1.0 / LGPL-2.1 (Logback), MIT (SLF4J) |
| Jakarta EE / JAXB (Eclipse) | APIs Java | EPL-2.0 / BSD-3-Clause |
| Argon2 (vía Spring Security Crypto + Bouncy Castle) | Hash de contraseñas | Apache-2.0 / MIT |

## Panel web (React)

React, React Router, Vite, Tailwind CSS, Radix UI, class-variance-authority, lucide-react: licencias **MIT** o **ISC**.

## Imagen Docker

Se basa en `eclipse-temurin` (OpenJDK, GPL-2.0 con Classpath Exception) sobre Ubuntu.

> Esta lista resume las licencias de las dependencias directas más relevantes. Si detectas un error u omisión, avísalo
> en https://github.com/ederaira/yapa-releases/issues y se corregirá.
