# Despliegue en intranet — CAU OS v2.0

## 1. Servidor recomendado

Para un piloto puede utilizarse un Windows 11/Windows Server corporativo siempre que permanezca disponible para los técnicos. Para uso estable, es preferible una VM/servidor interno administrado por Sistemas.

Carpeta recomendada:

```text
C:\CAU-OS-Server\
```

No ejecute el programa desde una carpeta temporal, desde el ZIP ni desde el perfil de un usuario que pueda eliminarse.

## 2. Cuenta de servicio

Use una cuenta Windows dedicada de mínimos privilegios. Debe disponer de:

- lectura/escritura/modificación sobre `C:\CAU-OS-Server`;
- lectura sobre las rutas UNC/locales que un Administrador vaya a vincular;
- permiso de escucha en el puerto elegido según las políticas del sistema;
- **no necesita ser Administrador local por diseño**.

## 3. Primer arranque

Ejecute `CAU-OS.exe` desde el servidor. El valor por defecto es:

```text
0.0.0.0:8090
```

Desde el mismo servidor abra:

```text
http://localhost:8090/
```

Cree el primer administrador. Una petición remota no puede realizar este bootstrap.

## 4. Firewall

Cree en **Windows Defender Firewall with Advanced Security** una regla de entrada:

- Tipo: Puerto.
- TCP.
- Puerto local: `8090` (o el elegido).
- Acción: Permitir conexión.
- Perfil: **Domain** únicamente, salvo necesidad justificada.
- Ámbito remoto: limite a la subred/VLAN del CAU cuando sea posible.

No desactive el firewall completo.

## 5. Acceso de usuarios

Con IP privada:

```text
http://192.168.10.25:8090/
```

Con hostname de una sola etiqueta:

```text
http://CAU-SERVER:8090/
```

Para un FQDN como:

```text
cau-os.empresa.local
```

añada `cau-os.empresa.local` a `trustedHosts` en `config/server.json` o **Administración > Servidor** y reinicie CAU OS.

## 6. HTTPS/TLS para producción

Coloque un certificado y clave privada en formato PEM en una ubicación protegida; por ejemplo:

```text
config/server.crt
config/server.key
```

Configure:

```json
{
  "tlsCertFile": "config/server.crt",
  "tlsKeyFile": "config/server.key"
}
```

Al reiniciar, el acceso será `https://...` y la cookie de sesión se marcará `Secure`.

La emisión/renovación del certificado debe integrarse en la PKI corporativa. No se incluye una CA ni certificados autofirmados en el ZIP.

## 7. Usuarios

Desde **Administración > Usuarios** cree las cuentas necesarias. Recomendación:

- técnicos que solo consultan/validan: Técnico;
- mantenedores de documentación: Editor;
- 2 administradores como mínimo para evitar dependencia de una única persona.

## 8. Documentación

### Importada

Un Editor puede subir archivos desde su navegador. El servidor conserva una copia dentro de `library/`.

### Ruta vinculada

Solo un Administrador puede configurar una ruta local/UNC del propio servidor. CAU OS indexa archivos compatibles sin copiar los originales. La cuenta Windows que ejecuta CAU OS debe tener acceso de lectura.

Ejemplo:

```text
\SERVIDOR-FICHEROS\CAU\Documentacion
```

## 9. Backups

Por defecto:

- intervalo: 24 h;
- retención local: 14 copias.

Los backups se almacenan en:

```text
exports\backups\
```

Para protección real frente a pérdida del servidor, copie esos ZIP mediante una herramienta corporativa a un almacenamiento independiente. CAU OS no envía backups por red automáticamente.

## 10. Inicio automático

Para un servidor permanente puede usarse **Task Scheduler**:

- Trigger: At startup.
- Cuenta: cuenta dedicada de CAU OS.
- Programa: `C:\CAU-OS-Server\CAU-OS.exe`.
- Argumento: `--no-browser`.
- Start in: `C:\CAU-OS-Server`.

Pruebe la tarea después de un reinicio. No se incluye un script BAT/PowerShell de instalación para reducir superficie de seguridad y alertas heurísticas.

## 11. Validación antes de producción

- iniciar sesión desde al menos dos equipos;
- comprobar Lector/Técnico/Editor/Admin;
- cargar documentos reales de muestra;
- buscar frases, errores y códigos;
- descargar la fuente desde un equipo remoto;
- comprobar backup y restauración en un entorno de prueba;
- validar certificado TLS;
- validar Defender/EDR/AppLocker/WDAC;
- realizar un soak de varios días con el corpus real.

## 12. No hacer

- No publicar NAT/port-forwarding hacia Internet.
- No ejecutar como Domain Admin.
- No compartir una cuenta Administrador entre todo el CAU.
- No desactivar EDR/Defender para evitar avisos.
- No guardar el único backup en el mismo disco que CAU OS.
