# 09 · Alta disponibilidad

[⬅ README](../README.md)

**Respuesta honesta:** no hay alta disponibilidad; es un entorno DEV con presupuesto de estudiante.

| Punto único de falla | Impacto | Mitigación en producción |
|---|---|---|
| VPN001 | Nadie entra a la red privada | 2 instancias / Azure VPN Gateway |
| BAS001 | Sin administración (salvo `az vm run-command`) | 2 bastiones / Azure Bastion |
| DKR001 | Toda la web, proxy filtro, DNS, NTP, NOC, PKI, Samba, backups | Repartir en varios hosts; Application Gateway |
| DKR002 | PBX/IVR, correo, CMS, mesa, NVR | Ídem |
| SQL001 | Todas las aplicaciones | Réplica / Azure Database for PostgreSQL |
| AD001 | Autenticación VPN y dominio | Segundo controlador de dominio |
| SIE001 | Detección | Clúster Wazuh |

Mitigaciones existentes: `unless-stopped` en contenedores; NTP con PHC0 de respaldo en clientes; backups cada 15 min (parcial).
Zonas de disponibilidad: **no usadas**.
