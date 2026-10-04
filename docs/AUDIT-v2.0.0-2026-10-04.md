# Auditoría técnica — CAU OS v2.0.0 Knowledge Server

Fecha: 2026-10-04

## Veredicto

**Arquitectura: sólida. Release binaria actual: NO APROBADA para producción excelente.**

La causa principal es la cadena de compilación: `CAU-OS.exe` declara `go1.23.2`. Go 1.23 está fuera de la ventana de soporte actual y recibió múltiples correcciones posteriores, incluidas correcciones de seguridad. El paquete puede usarse para laboratorio/piloto controlado, pero la build debe recompilarse desde el código fuente con una versión de Go soportada y repetir QA/seguridad antes de producción.

## Evidencia reproducida sobre el paquete

- Formato: PE32+ x86-64 GUI para Windows.
- Build info embebida: `go1.23.2`, `CGO_ENABLED=0`, `GOOS=windows`, `GOARCH=amd64`, `-trimpath=true`.
- ASLR/NX: `DYNAMIC_BASE`, `HIGH_ENTROPY_VA`, `NX_COMPAT` presentes.
- Authenticode: no existe Security Directory/firma embebida.
- `sha256sum -c SHA256SUMS.txt`: todos los archivos declarados PASS.
- SHA-256 del EXE: `57d34e60a63a71b48dbdab1c365ee427a74a029ecdbd969d84dfb753e38a432b`.
- El paquete no contiene el código fuente Go, por lo que no es posible reparar o verificar internamente handlers, validaciones y controles de autorización con revisión de source.

## Hallazgos

### CAU-SEC-001 — ALTO — Runtime Go obsoleto/no soportado

**Evidencia:** `go version -m CAU-OS.exe` devuelve `go1.23.2`.

**Impacto:** la build no incorpora correcciones posteriores del runtime/stdlib y no cumple un criterio razonable de mantenimiento de seguridad para producción en 2026.

**Corrección requerida:** recompilar desde fuente con Go 1.26 o 1.27 actualizado, ejecutar tests/race/vet/smoke, revisar dependencias y generar un binario nuevo firmado.

**Estado:** BLOQUEADO por ausencia del código fuente en la entrega.

### CAU-SEC-002 — ALTO — HTTP LAN antes de TLS en el flujo original

**Evidencia:** configuración original `0.0.0.0:8090` sin certificado y documentación que mostraba URLs `http://IP:8090/` para usuarios remotos.

**Impacto:** si se usa tal cual en una red compartida, credenciales y contenido pueden viajar sin cifrar. Una intranet no elimina el riesgo de sniffing/MITM.

**Corrección aplicada al paquete endurecido:** `config/server.json` escucha únicamente en `127.0.0.1`. Se incorpora una plantilla de producción y la documentación exige configurar TLS + firewall antes de habilitar escucha LAN.

**Estado:** MITIGADO a nivel de paquete/configuración. Debe verificarse en Windows real.

### CAU-SEC-003 — MEDIO — Clave TLS susceptible de incluirse en backups

**Evidencia:** la documentación de seguridad indica que los backups pueden incluir material TLS si está dentro de `config/`.

**Impacto:** multiplica copias de la clave privada y amplía la superficie de exposición.

**Corrección aplicada:** plantilla de producción sitúa certificado/clave fuera de la carpeta de aplicación (`C:\ProgramData\CAU-OS\tls\...`) y el checklist exige ACL.

**Estado:** MITIGADO documentalmente/configuración de ejemplo.

### CAU-SEC-004 — MEDIO — Binario sin Authenticode

**Evidencia:** PE sin Security Directory y limitación reconocida por el propio paquete.

**Impacto:** SmartScreen/EDR puede bloquearlo y el usuario no dispone de autenticidad criptográfica del editor.

**Corrección requerida:** firma de código corporativa o allowlisting formal basado en hash/cadena de suministro.

**Estado:** PENDIENTE del entorno corporativo.

### CAU-QA-001 — MEDIO — No reproducibilidad completa de los gates declarados

**Evidencia:** `docs/BUILD-METADATA-v2.0.0.txt` declara PASS de tests, race, vet y smoke, pero el ZIP no contiene source ni tests para repetirlos.

**Impacto:** la evidencia histórica es útil, pero no puede ser revalidada independientemente desde esta entrega.

**Corrección requerida:** conservar un paquete/source tag reproducible o CI con artefactos y logs firmados.

**Estado:** PENDIENTE.

### CAU-OPS-001 — BAJO — Auditoría JSONL no WORM

**Evidencia:** limitación documentada.

**Impacto:** un administrador del host podría alterar logs locales.

**Recomendación:** para requisitos fuertes de trazabilidad, exportar/recoger logs en SIEM o almacenamiento inmutable corporativo.

**Estado:** ACEPTABLE para v2.0 si se documenta.

## Puntos fuertes confirmables por inspección

- Diseño local/intranet sin dependencia cloud obligatoria.
- Roles diferenciados Reader/Technician/Editor/Admin.
- Bootstrap inicial documentado como loopback-only.
- PBKDF2-HMAC-SHA256, cookies HttpOnly/SameSite y CSRF declarados.
- Separación de lectura, edición y administración de rutas UNC.
- Endpoints heredados de scripts/remediación declarados fuera de esta edición.
- Backups y auditoría incluidos en el diseño.
- PE con ASLR de alta entropía y NX.
- Manifiesto SHA-256 de integridad íntegro.

## Gates

- Architecture Gate: PASS por inspección documental.
- Package Integrity Gate: PASS.
- Static Binary Hardening Gate: PASS parcial (ASLR/NX sí; Authenticode no).
- Security Gate: FAIL por CAU-SEC-001 hasta recompilar.
- Windows Runtime Gate: PENDIENTE (no se ejecutó Windows/Defender/EDR en este entorno).
- Release Gate: BLOCKED para STABLE/producción excelente.

## Objetivo de siguiente release

Crear **CAU OS v2.0.1** desde el source original, sin introducir funciones nuevas:

1. toolchain Go soportada y actualizada;
2. mismas funciones v2.0;
3. default loopback seguro;
4. TLS antes de LAN;
5. firma Authenticode cuando sea posible;
6. QA reproducible y logs de CI;
7. smoke Windows real;
8. nuevo manifiesto SHA-256.
