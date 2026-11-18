# 04 · Contenedores Docker

[⬅ README](../README.md) · [Contenedor detenido](../ESCENARIOS_DE_FALLO/10_DOCKER_CONTENEDOR_DETENIDO.md)

**21 contenedores de rol único:** 12 en DKR001, 9 en DKR002. Docker Compose por servicio. Política `unless-stopped` verificada en NOC001, PKI001, MSA001.

### PREGUNTA DK1 · ¿Qué componentes comparten DKR002?
**🗣️ ORAL:** "DKR002 aloja PBX e IVR de Daniel, CMS Drupal de Daniel, correo de Pulido (Postfix, Roundcube y su proxy TLS), la mesa de ayuda de Nicolás y el NVR de Esteban con su cámara simulada. Comparten 2 vCPU y 8 GiB y la IP 10.1.100.102; cada uno publica su propio puerto."
| Contenedor | Integrante |
|---|---|
| PBX001, IVR001, CMS001 | Daniel |
| MTA001, WBM001, HTTPSWBM001 | Pulido |
| MSA001 | Nicolás |
| NVR001, CAM001 | Esteban |
**🖥️** `[DKR002]` `sudo docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}"` 🟢

### PREGUNTA DK2 · ¿Qué pasa si DKR001 se apaga?
LBR001 (toda la web: pruebas 7, 9, 10 y acceso web de 2), LMS, filtro PRX001 (4, 8), DNS001/003, NTP central, NOC (6), PKI, Samba, backups, RADIUS. Siguen: VPN, AD, SIEM, ERP (sin acceso web), DKR002 (PBX/IVR funcionan; P1 sí).

### PREGUNTA DK3 · ¿Un contenedor es un "servidor"?
"Para el diseño, sí: tiene un solo rol, su imagen, su puerto y su configuración; comparte kernel, CPU y RAM del host. El diagrama los dibuja dentro de su VM."

### PREGUNTA DK4 · ¿Por qué las redes Docker 172.x no están en el diagrama como subredes?
Son puentes locales del host, no subredes de Azure.
