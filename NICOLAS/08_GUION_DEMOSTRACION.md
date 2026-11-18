# 08 · Guion de demostración — Nicolás (escenas 5 y 6)

[⬅ Índice Nicolás](00_INDICE_NICOLAS.md)

| # | Pantalla | Acción | Qué explicar |
|---|---|---|---|
| 1 | `[AD001/POWERSHELL]` | OUs y grupos ([01](01_ACTIVE_DIRECTORY.md)) | Separación organizativa y mínimo privilegio |
| 2 | `[AD001/POWERSHELL]` | Usuarios operario1/soporte1 | Identidades de prueba; vendedor1 preservado por la VPN |
| 3 | `[AD001/POWERSHELL]` | Vínculo de GPO y valor 900 ([02](02_GPO.md)) | Configurada; no fingir aplicación |
| 4 | `[DKR001]` | `docker ps` PKI + `/health` con `--cacert` ([03](03_PKI_CERTIFICADOS.md)) | Emisión interna y confianza TLS |
| 5 | `[DKR001]` | smbclient soporte1 / operario1 ([04](04_SERVIDOR_ARCHIVOS.md)) | Solo lectura y denegación |
| 6 | `[NAVEGADOR]` | Redmine ticket #1 ([05](05_MESA_AYUDA_REDMINE.md)) | Mesa operativa con PostgreSQL fuera del host |
| 7 | `[DKR001]` | cron, logs, restauración 58 tablas ([06](06_BACKUPS_RESTIC.md)) | La copia se recupera sin tocar producción |
| 8 | — | Cierre | Pendientes: LDAPS/SSO, GPO en cliente, backup ERP y off-host, SMB intersubredes |

**Ruta de acceso:** VPN → túnel RDP 13389 por bastión → AD001; en otra terminal `ssh dkr1` / `ssh dkr2` (o `-J` desde Windows). Confirmar `hostname; date -Is` antes de cada comando.

**Plan B:** capturas fechadas de comandos de solo lectura, ticket, GPO y restauración.
