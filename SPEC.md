# Especificación funcional — CAU OS v2.0

## Producto
Base de conocimiento documental centralizada para soporte interno.

## Prioridades
1. Encontrar una solución rápidamente.
2. Mantener una única biblioteca compartida.
3. Mostrar fuente y pasaje relevante.
4. Evitar ejecución de scripts/remediaciones.
5. Controlar quién puede leer, validar, editar y administrar.
6. Poder recuperar el servidor mediante backups.

## Roles
Reader / Technician / Editor / Admin.

## Red
Servidor en intranet; puerto 8090 configurable. TLS directo opcional pero recomendado/obligatorio para producción según política corporativa. No Internet.

## Persistencia
Documentos, estado, índice, configuración, logs/auditoría y backups bajo la carpeta de CAU OS.

## Fuera de alcance v2.0
AD/Entra SSO, OCR, RAG/LLM, Internet, ITSM, ejecución de scripts, agente remoto, alta disponibilidad multinodo.
