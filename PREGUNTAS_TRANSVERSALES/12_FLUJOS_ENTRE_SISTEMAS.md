# 12 · Flujos entre sistemas (preguntas de recorrido)

[⬅ README](../README.md) · [Arquitectura](../04_ARQUITECTURA_GENERAL.md)

Cada flujo: **integrantes · servidores · evidencia**.

### F1 · Vendedor abre el ERP
VEN001 → UDP 1194 → VPN001 (TOTP + sssd → AD001) → 10.8.0.x/NAT 10.1.110.10 → LBR001:443 (DKR001) → ERP001:8069 → SQL001:5432.

**👥** Escobar, Nicolás, Daniel · **Evidencia:** P2 pasos 1-5 ✅, paso 6 ⏳.

### F2 · Cliente consulta pedido por IVR
MicroSIP → VPN → PBX001:5060 (DKR002) → IVR001:5062 → SQL001:5432 (`ivr_ro`).

**👥** Daniel, Escobar (NSG 220-240, 155) · **Evidencia:** P1 ✅.

### F3 · Estudiante abre el LMS
Internet → 172.212.220.169:443 → LBR001 → LMS001:8082 → SQL001 (158). **👥** Daniel · ✅.

### F4 · Empleado publica en la intranet
VPN → LBR001 (allow 10.1.0.0/16) → CMS001:8083 (DKR002, regla 210) → SQL001 (155). **👥** Daniel · 🟠.

### F5 · Administrador entra a AD001 (VPN → bastión → AD001)
**🗣️ ORAL (≤20 s):** "Conecto la VPN con AD y TOTP; desde mi equipo abro un túnel SSH al bastión que reenvía mi puerto local 13389 al 3389 de AD001; el NSG de Chile Central solo acepta RDP desde la IP del bastión, regla 175. Luego abro Escritorio remoto contra localhost:13389."

**👥** Escobar (VPN, NSG), Esteban (bastión), Nicolás (AD) · **🖥️** `[MAC]` → `[BASTION]` → `[AD001]`

**⌨️** `ssh -o ExitOnForwardFailure=yes -N -L 13389:10.1.100.100:3389 bastion` + `nc -vz 127.0.0.1 13389` (🟢) · **✅** `succeeded` · **📌** túnel ✅; sesión autenticada reportada por consolidado.

### F6 · Evento SSH llega a Wazuh
sshd (BAS001) → `/var/log/auth.log` → agente 001 → TCP 1514 (regla r3 190) → manager SIE001 → reglas (p. ej. 5710) → `alerts.json`. **👥** Esteban · ✅.

### F7 · Métrica de CPU llega a Grafana
node_exporter :9100 (host) ← scrape cada 15 s ← Prometheus (DKR001) → Grafana (DKR001) ← túnel SSH ← navegador. **👥** Esteban, Escobar (NSG) · ✅.

### F8 · Hora del bastión
BAS001 chrony → UDP 123 (regla 125) → NTP001 (DKR001) ← PHC0 de Azure. **👥** Esteban · ✅.

### F9 · Navegación del vendedor filtrada
VEN001 (Edge con proxy) → VPN → 10.1.100.41:3128 (PRX001, regla 129 ⏳) → Internet / 403; log en `access.log`. **👥** Pulido, Escobar · 🟠.

### F10 · Correo interno y bloqueo externo
VEN001 → VPN → HTTPSWBM001 → WBM001 → MTA001 (Postfix) → buzón Dovecot / 554 externo. **👥** Pulido, Escobar (regla 300 ⏳) · 🟠.

### F11 · Backup de la mesa de ayuda
cron (DKR001) → pg_dump SQL001:5432 base msa → restic → rest-server 127.0.0.1:8000 → volumen local. **👥** Nicolás · ✅.

### F12 · Cámara al NVR
CAM001 FFmpeg → rtsp://127.0.0.1:8554/camara1 → NVR001 MediaMTX (DKR002). **👥** Esteban · ✅.

### Preguntas cruzadas rápidas
| Pregunta | Integrantes | Respuesta corta | Archivo |
|---|---|---|---|
| ¿Qué ocurre si DKR001 se apaga? | Todos | Cae la web, filtro, DNS, NTP central, NOC, PKI, Samba, backups | [04 DK2](04_CONTENEDORES_DOCKER.md) |
| ¿Qué componentes comparten DKR002? | Daniel, Pulido, Nicolás, Esteban | PBX/IVR/CMS, correo, Redmine, NVR | [04 DK1](04_CONTENEDORES_DOCKER.md) |
| ¿Cómo afecta NTP a Kerberos y SIEM? | Esteban, Nicolás | 5 min de tolerancia Kerberos; correlación | [06 ID2](06_IDENTIDAD_Y_AUTENTICACION.md) |
| ¿GPO configurada vs aplicada? | Nicolás | Vínculo vs gpresult en cliente | [NICOLAS/02](../NICOLAS/02_GPO.md) |
| ¿Dónde termina TLS? | Daniel | LBR001 | [07](07_CERTIFICADOS_Y_CIFRADO.md) |
| ¿Si falla PostgreSQL? | Daniel, Nicolás | Caen todas las apps | [03](03_BASES_DE_DATOS.md) |
| ¿Backup restaurable? | Nicolás | 58 tablas | [08](08_BACKUP_Y_RECUPERACION.md) |
| ¿Tráfico RTSP? | Esteban | Logs MediaMTX, loopback | [ESTEBAN/07](../ESTEBAN/07_NVR_MEDIAMTX_FFMPEG.md) |
| ¿Qué reglas NSG permiten los flujos? | Escobar | Tablas por NSG | [SANTIAGO/02](../SANTIAGO/02_VNET_SUBREDES_NSG.md) |
| ¿Problemas reales y soluciones? | Todos | Ver tabla | [REFERENCIAS/CAMBIOS_RECIENTES.md](../REFERENCIAS/CAMBIOS_RECIENTES.md) |
