CAU OS v2.0 - CONFIGURACION DEL SERVIDOR

El archivo server.json controla el modo de escucha, puerto, sesiones, copias de seguridad y TLS.
La configuracion tambien puede modificarse desde Administracion > Servidor.

IMPORTANTE:
- No exponga CAU OS directamente a Internet.
- Para un FQDN corporativo, anadalo a trustedHosts.
- Para produccion, configure TLS con certificado y clave PEM.
- La clave privada TLS y los backups deben protegerse con ACL de Windows.

HARDENING 2026-10-04:
- El server.json incluido escucha por defecto solo en 127.0.0.1.
- No cambies a 0.0.0.0 hasta haber preparado TLS y Firewall.
- Usa server.production.example.json como plantilla de produccion.
- Es preferible mantener la clave TLS fuera de la carpeta de CAU OS para evitar que se duplique dentro de backups de la aplicacion.
