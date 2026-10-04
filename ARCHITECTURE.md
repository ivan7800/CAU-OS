# Arquitectura — CAU OS v2.0

```text
 Navegadores del CAU
        │
        │ HTTP(S) intranet
        ▼
┌─────────────────────────────┐
│ CAU OS Knowledge Server     │
│                             │
│ Auth + RBAC + CSRF          │
│ Search / FTS / passages     │
│ Library / Solutions         │
│ Audit / Backups             │
└────────────┬────────────────┘
             │
    ┌────────┼───────────────┐
    ▼        ▼               ▼
 data/    library/       rutas UNC
 estado   importados     (solo Admin)
```

La aplicación es un único binario Go con frontend HTML/CSS/JS embebido. No requiere Node, Python, IIS ni una base de datos externa para ejecutar. La persistencia principal se mantiene bajo la carpeta del servidor; SQLite/FTS actúa como índice derivado de búsqueda.

El servidor registra únicamente los módulos de biblioteca, indexación, base de conocimiento, soluciones, búsquedas guardadas, backups, autenticación y administración. Los módulos heredados de ejecución/diagnóstico/tickets no se registran en Knowledge Server.
