# CAU OS v2.0.0 — Knowledge Server

CAU OS es una **base de conocimiento y biblioteca documental centralizada para equipos de soporte**. Un único servidor mantiene la documentación, el índice de búsqueda, las fichas de solución, los usuarios, la auditoría y las copias de seguridad. Los compañeros acceden desde el navegador.

## Objetivo

El flujo de uso es deliberadamente corto:

**abrir CAU OS → describir el problema → localizar el fragmento relevante → abrir la fuente → resolver**.

No requiere ServiceNow, Jira ni Remedy. La edición v2.0 tampoco registra rutas de ejecución de scripts, diagnósticos ni remediaciones.

## Funciones principales

- Biblioteca central para PDF, DOCX, XLSX, PPTX, ODT/ODS, RTF, HTML, TXT, Markdown, CSV, LOG, JSON, XML y YAML compatibles.
- Indexación local del contenido y búsqueda por texto/pasajes.
- Fichas de solución con validación `Me sirvió / No me sirvió`.
- Documentos importados al servidor o rutas documentales vinculadas por un administrador.
- Usuarios y roles: Lector, Técnico, Editor y Administrador.
- Inicio de sesión con sesiones temporales y contraseñas derivadas con PBKDF2-HMAC-SHA256 + sal aleatoria.
- Auditoría de cambios en `logs/audit.jsonl`.
- Copias de seguridad automáticas y restauración administrada.
- Skins y modo claro/oscuro por navegador/usuario.
- Funcionamiento sin IA externa y sin enviar la documentación a servicios cloud.

## Arranque rápido

1. Copie la carpeta completa a un servidor Windows, por ejemplo `C:\CAU-OS-Server`.
2. Ejecute `CAU-OS.exe` **desde el propio servidor**.
3. Abra `http://localhost:8090/` y cree el primer administrador.
4. Cree las cuentas del equipo desde **Administración > Usuarios**.
5. Desde otro equipo de la misma red, abra `http://IP-DEL-SERVIDOR:8090/`.
6. Antes de producción, configure HTTPS/TLS y una regla de firewall limitada a la red/VLAN autorizada.

Lea `INICIAR_SERVIDOR.txt` y `docs/DESPLIEGUE-INTRANET-v2.0.md` antes del despliegue general.

## Seguridad importante

- El primer administrador **solo puede crearse desde loopback** (`127.0.0.1`/`::1`).
- No publique el puerto de CAU OS en Internet.
- Los documentos requieren autenticación; no se sirven como archivos estáticos públicos.
- Las rutas locales/UNC del servidor solo pueden ser vinculadas/sincronizadas por un Administrador.
- En Knowledge Server, la operación antigua de abrir un archivo en el escritorio del servidor está desactivada. El documento se entrega al navegador del usuario autorizado.
- Los backups contienen información sensible: documentos internos, configuración y hashes de contraseñas. Protégelos con ACL y almacenamiento corporativo autorizado.

## Publicación en GitHub

Este repositorio distribuye la build binaria **CAU OS v2.0.0** junto con documentación, configuración segura de ejemplo y manifiesto SHA-256. El código fuente Go no forma parte de esta distribución; por tanto, el repositorio no pretende ofrecer una build reproducible desde source.

Antes de ejecutar una descarga, verifique su integridad desde PowerShell:

```powershell
(Get-FileHash .\CAU-OS.exe -Algorithm SHA256).Hash.ToLower()
```

El resultado esperado para la build v2.0.0 incluida es:

```text
57d34e60a63a71b48dbdab1c365ee427a74a029ecdbd969d84dfb753e38a432b
```

La automatización de GitHub comprueba el manifiesto de integridad, que el bind por defecto siga siendo loopback-only, que no se incorporen claves privadas y que las carpetas de runtime estén vacías. Esto **no sustituye** la recompilación desde source ni la validación corporativa de Windows descrita en la auditoría.

## Estado de producción

El software se valida automáticamente en Linux y se compila para Windows x64. El comportamiento final de Defender/EDR, AppLocker/WDAC, certificados corporativos, firewall y rendimiento con el corpus real debe verificarse en el entorno de la empresa antes del despliegue general.

## Nota de auditoría 2026-10-04

Este paquete endurecido cambia el valor inicial de `bindAddress` a `127.0.0.1` para que una instalación recién extraída no exponga HTTP sin TLS a la LAN. Utilice `config/server.production.example.json` como referencia y habilite acceso remoto únicamente después de preparar HTTPS y firewall.

La build `CAU-OS.exe` incluida sigue siendo exactamente la de v2.0.0 y fue compilada con Go 1.23.2. No se ha modificado ni se presenta como una recompilación. Para una release de producción excelente debe recompilarse desde el código fuente con una versión Go soportada y repetirse QA/seguridad. Consulte `docs/AUDIT-v2.0.0-2026-10-04.md`.
