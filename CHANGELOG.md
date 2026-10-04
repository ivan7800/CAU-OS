
## 2.0.0 hardened — GitHub packaging revision (2026-10-04)

- Corrige las Release Notes para reflejar `127.0.0.1:8090` como bind seguro por defecto.
- Añade `.gitignore` para evitar datos, logs, backups, bases de datos y material TLS accidental.
- Añade `.gitattributes`.
- Añade licencia explícita para distribución binaria.
- Añade workflow de GitHub Actions para integridad del paquete y controles de publicación.
- Añade checklist de release para GitHub.
- No modifica `CAU-OS.exe`; la versión del binario continúa siendo v2.0.0 y su SHA-256 permanece sin cambios.


## 2.0.0 — Knowledge Server — 2026-10-02
- Servidor central de intranet.
- Usuarios y RBAC.
- Bootstrap local del primer administrador.
- PBKDF2 y sesiones seguras.
- Administración de usuarios.
- Auditoría de cambios.
- Backups automáticos.
- Configuración TLS y host confiable.
- Documentos servidos al navegador remoto.
- Rutas del servidor/UNC reservadas a Admin.
- Apertura local del servidor desactivada.
- Mantiene Knowledge Base, biblioteca, soluciones y skins de v1.8.1.
