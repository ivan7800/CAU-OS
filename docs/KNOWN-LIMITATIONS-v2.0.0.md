# Limitaciones conocidas — v2.0.0

- El ejecutable Windows no está firmado con Authenticode corporativo; EDR/SmartScreen puede aplicar reputación o bloquearlo según política.
- CAU OS no instala ni modifica automáticamente reglas de firewall.
- TLS requiere certificado y clave en formato PEM y su ciclo de vida lo gestiona la organización.
- Las sesiones viven en memoria y se pierden al reiniciar el servidor; los usuarios simplemente vuelven a iniciar sesión.
- No hay integración AD/Entra/LDAP/SSO en v2.0: las cuentas se gestionan localmente en CAU OS.
- Los favoritos y búsquedas guardadas siguen siendo conocimiento compartido, no preferencias privadas por usuario.
- La indexación no usa OCR para documentos escaneados sin capa de texto.
- Algunos formatos complejos pueden perder estructura durante la extracción; el original siempre sigue disponible como fuente.
- Las rutas UNC dependen de los permisos de la cuenta Windows que ejecuta CAU OS.
- El servidor no debe exponerse directamente a Internet.
- La restauración de backup se prepara y requiere reiniciar CAU OS.
- La auditoría registra quién/qué endpoint/cuándo/IP/estado; no implementa firma criptográfica WORM de los logs.
