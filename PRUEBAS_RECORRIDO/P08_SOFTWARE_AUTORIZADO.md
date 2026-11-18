# Prueba 8 · Software autorizado (grado 2) — 🟠

[⬅ Índice pruebas](00_INDICE_PRUEBAS.md) · [Samba](../NICOLAS/04_SERVIDOR_ARCHIVOS.md) · [Squid](../OTROS_INTEGRANTES/PULIDO/01_PROXY_FILTRO_SQUID.md)

**Rúbrica:** "Se demuestra que se deniega la descarga de software no autorizado a los empleados de LANgustino."

**Responsables:** Pulido (bloqueo en proxy) + Nicolás (catálogo en Samba). **Estado:** AnyDesk 403 desde DKR001 ✅ · FIL001 lectura/denegación ✅ (local) · desde VEN001 ⏳.

| # | Dónde | Hacer | Ver |
|---|---|---|---|
| 1 | `[VEN001]` Edge (VPN + proxy) | https://anydesk.com/es/downloads | Error del proxy |
| 2 | `[VEN001]` | https://www.teamviewer.com | Bloqueado |
| 3 | `[DKR001]` 🟢 | `sudo docker exec SRVD1DEVPRX001 sh -c "tail -n 20 /var/log/squid/access.log" \| grep DENIED` | `TCP_DENIED/403` anydesk / teamviewer |
| 4 | `[DKR001]` 🟢 | `smbclient //10.1.100.41/software -U soporte1 -c "ls"` (clave cuando la pida) | Lista (hoy solo `LEEME.txt`, 34 B) |
| 5 | `[DKR001]` 🟢 | `smbclient //10.1.100.41/software -U operario1 -c "ls"` | `NT_STATUS_ACCESS_DENIED` |

**Qué decir:** "El proxy tiene en su lista los sitios de acceso remoto no autorizado (anydesk, teamviewer, rustdesk): el empleado no los puede descargar y el intento queda en el log. El software permitido se publica desde el servidor de archivos SRVD1DEVFIL001, en una carpeta de solo lectura: soporte la lee, un operario no tiene acceso y nadie puede escribir."

**Honestidad:** la carpeta solo tiene `LEEME.txt`; no decir que hay un catálogo completo. Las cuentas Samba son locales, no del AD. SMB desde DKR002 → DKR001:445 dio **timeout**.

Plan B: `curl -x http://10.1.100.41:3128 https://www.anydesk.com` desde DKR001 → 403 (probado por Pulido).
