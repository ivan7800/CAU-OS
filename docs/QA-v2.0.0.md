# QA — CAU OS v2.0.0

## Gates automáticos

Antes de empaquetar la release:

```text
go test ./...
go test -race ./...
go vet ./...
node --check web/app.js
```

## Smoke funcional requerido

- `GET /api/health` sin sesión.
- Carga de la interfaz de login sin sesión.
- Bootstrap de primer admin por loopback.
- Segundo bootstrap rechazado.
- Login Admin/Lector/Editor.
- Lector puede consultar y no puede importar.
- Editor puede importar e indexar.
- Lector puede buscar el pasaje importado.
- Reset de contraseña invalida sesión anterior.
- Auditoría atribuye cambios al usuario.
- Endpoint de apertura local devuelve 410.
- Editor no puede crear/sincronizar ruta local/UNC del servidor.

## Gates manuales corporativos

- Defender/EDR.
- AppLocker/WDAC.
- Windows Firewall y segmentación de red.
- Edge/Chrome corporativos.
- Certificado TLS corporativo.
- ACL de la carpeta y backups.
- UNC bajo la cuenta de servicio real.
- Rendimiento/soak con el corpus de producción.
