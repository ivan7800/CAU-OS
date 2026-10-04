# Release Notes — CAU OS v2.0.0 Knowledge Server

## Nuevo

- Servidor central para intranet con arranque endurecido en `127.0.0.1:8090` por defecto. La escucha LAN se habilita explícitamente después de configurar TLS y firewall.
- Autenticación de usuarios y RBAC: Reader / Technician / Editor / Admin.
- Primer administrador limitado a loopback.
- PBKDF2-HMAC-SHA256 con sal aleatoria y 210.000 iteraciones.
- Sesiones temporales HttpOnly/SameSite=Strict, Secure con TLS.
- Rate limit básico de intentos de login.
- Administración de usuarios, activación/desactivación, roles y reset de contraseña.
- Auditoría JSONL por usuario/IP para mutaciones y eventos de autenticación.
- Backups automáticos configurables y retención.
- Configuración de servidor y TLS desde panel Admin.
- Soporte directo TLS con certificado/clave PEM.
- Descarga/apertura de documentos en el navegador remoto.
- Skins y apariencia almacenados en el navegador de cada usuario.

## Endurecimiento

- Rutas de scripts/remediaciones/diagnósticos siguen sin registrarse.
- La API antigua de apertura local del documento se desactiva (`410 Gone`).
- Las rutas locales/UNC del servidor solo pueden vincularse/sincronizarse por Administradores.
- Validación Host/Origin/Sec-Fetch-Site y token anti-CSRF.
- Último Administrador activo protegido frente a degradación/desactivación accidental.
- Reset de contraseña/desactivación invalida sesiones del usuario.

## Compatibilidad

La biblioteca y el estado de v1.8.x pueden migrar a schema 17. Antes de migrar una instalación con datos reales, realice una copia de la carpeta completa.
