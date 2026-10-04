# Checklist de preproducción — CAU OS v2.0.0

No marque la instalación como producción hasta completar los puntos aplicables.

## Binario y cadena de compilación

- [ ] Recompilar el código fuente con una versión de Go actualmente soportada (Go 1.26/1.27 a fecha 2026-10-04).
- [ ] Ejecutar `go test ./...`.
- [ ] Ejecutar `go test -race ./...` en plataforma compatible.
- [ ] Ejecutar `go vet ./...`.
- [ ] Ejecutar las pruebas funcionales de `docs/QA-v2.0.0.md`.
- [ ] Generar de nuevo `SHA256SUMS.txt` después de compilar/empaquetar.
- [ ] Firmar `CAU-OS.exe` con Authenticode corporativo o completar allowlisting EDR.

## Red y TLS

- [ ] Mantener `bindAddress: 127.0.0.1` durante bootstrap y preparación.
- [ ] Instalar certificado TLS corporativo y proteger su clave privada mediante ACL.
- [ ] Preferir guardar la clave privada TLS fuera de la carpeta de CAU OS para que no termine dentro de backups de aplicación.
- [ ] Añadir el FQDN real a `trustedHosts`.
- [ ] Cambiar a `0.0.0.0` solo cuando HTTPS y firewall estén preparados.
- [ ] Firewall: perfil Domain y ámbito remoto restringido a la VLAN/subred autorizada.
- [ ] Confirmar que el puerto no está publicado por NAT/Internet.

## Cuentas y permisos

- [ ] Ejecutar con cuenta dedicada sin privilegios administrativos.
- [ ] ACL de instalación, `data`, `library`, `logs` y `exports/backups` revisadas.
- [ ] Dos administradores nominales; no compartir una cuenta Admin.
- [ ] Usuarios normales con Lector/Técnico; Editor solo para mantenedores.

## Pruebas funcionales

- [ ] Bootstrap remoto rechazado.
- [ ] Login correcto e incorrecto probado.
- [ ] Lector no puede escribir/importar.
- [ ] Editor puede importar/indexar, pero no gestionar raíces UNC/servidor.
- [ ] Admin puede gestionar usuarios y backups.
- [ ] Reset de contraseña invalida sesiones antiguas.
- [ ] Descarga documental requiere autenticación.
- [ ] Búsqueda devuelve fuente y pasaje correcto.
- [ ] Restore probado en copia/laboratorio, no por primera vez en producción.

## Operación

- [ ] Backup copiado a almacenamiento independiente del servidor.
- [ ] Restauración documentada y ensayada.
- [ ] Auditoría revisada y protegida por ACL.
- [ ] Defender/EDR/AppLocker/WDAC validados.
- [ ] Edge/Chrome corporativos validados.
- [ ] Soak con corpus real realizado.
