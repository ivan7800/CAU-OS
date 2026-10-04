# Seguridad — CAU OS Knowledge Server v2.0

## Modelo de seguridad

CAU OS v2.0 se diseña como aplicación de intranet, no como servicio público de Internet.

### Autenticación

- El primer administrador solo puede crearse desde una conexión loopback en el servidor.
- Contraseñas: PBKDF2-HMAC-SHA256, sal aleatoria por usuario y 210.000 iteraciones.
- Longitud mínima: 12 caracteres.
- Sesiones aleatorias almacenadas en memoria, con expiración configurable (8 h por defecto).
- Cookie `HttpOnly` y `SameSite=Strict`; se marca `Secure` cuando CAU OS usa TLS directamente.
- Cinco fallos de autenticación provocan bloqueo temporal de la combinación usuario/origen.
- Desactivar una cuenta o restablecer su contraseña invalida sus sesiones activas.
- La última cuenta administradora activa no puede degradarse/desactivarse accidentalmente.

### Autorización RBAC

- **Lector**: buscar, consultar y descargar documentación/soluciones.
- **Técnico**: Lector + favoritos/búsquedas guardadas/feedback de soluciones.
- **Editor**: Técnico + importar, indexar y editar documentos y fichas de solución.
- **Administrador**: Editor + usuarios, auditoría, backups, configuración, cierre del servidor y rutas locales/UNC vinculadas.

Las rutas locales o UNC se consideran acceso al sistema de archivos del servidor y por ello son exclusivamente administrativas.

### Web

- Protección same-origin, bloqueo de `Sec-Fetch-Site: cross-site` y validación de Host/Origin.
- Token anti-CSRF en las operaciones mutables.
- CSP restrictiva, `X-Frame-Options: DENY`, `X-Content-Type-Options: nosniff`, `Referrer-Policy: no-referrer`.
- La interfaz estática de login es pública; **todas las APIs de datos requieren una sesión y rol adecuados**.
- Los documentos se sirven solo a través de endpoints autenticados.

### Scripts y ejecución

La edición Knowledge Server **no registra** rutas de ejecución de scripts, remediaciones ni diagnósticos. La operación antigua de abrir un documento localmente en el escritorio del servidor devuelve `410 Gone`; los usuarios abren/descargan el archivo desde su navegador.

## TLS

Para producción configure `tlsCertFile` y `tlsKeyFile` en `config/server.json` con un certificado y clave privada PEM autorizados por la organización. Cuando ambos archivos son válidos, CAU OS sirve HTTPS directamente y las cookies de sesión usan `Secure`.

No exponga la instalación a Internet. Si se usa un FQDN (por ejemplo `cau-os.empresa.local`), añádalo a `trustedHosts`.

## Sistema operativo

- Ejecute CAU OS con una cuenta Windows dedicada y sin privilegios administrativos salvo que una política corporativa exija lo contrario.
- Conceda a esa cuenta acceso únicamente a la carpeta de CAU OS y a las rutas documentales vinculadas que realmente necesite.
- Limite la regla de firewall a perfil Domain y, preferiblemente, a la VLAN/subred del CAU.
- Aplique las políticas de EDR/Defender/AppLocker/WDAC de la organización; no las desactive para ejecutar CAU OS.

## Backups

Los backups pueden contener:

- biblioteca documental;
- estado y usuarios (incluidos hashes/sales de contraseñas, nunca contraseñas en claro);
- índice;
- configuración;
- auditoría;
- material TLS si se guarda dentro de `config/`.

Por tanto, un backup debe tratarse al menos con la misma clasificación que la documentación más sensible de la biblioteca. Guárdelo en almacenamiento autorizado, con ACL y, si procede, cifrado corporativo.

## Auditoría

Los eventos de autenticación relevantes y las mutaciones de API se registran en `logs/audit.jsonl` con fecha UTC, usuario, rol, método, ruta, estado e IP de origen. La vista de auditoría está reservada a Administradores.

## Limitaciones de seguridad conocidas

- El ejecutable Windows entregado no está firmado con un certificado Authenticode corporativo; SmartScreen/EDR puede aplicar reputación o política local.
- El TLS incorporado espera certificado/clave PEM; la gestión de PKI/renovación corresponde al entorno corporativo.
- Las sesiones son de memoria: un reinicio del servidor obliga a iniciar sesión de nuevo, lo que es una propiedad segura por defecto.
