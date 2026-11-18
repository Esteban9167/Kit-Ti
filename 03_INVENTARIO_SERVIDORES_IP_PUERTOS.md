# 03 · Inventario de servidores, IP y puertos

[⬅ README](README.md) · [Verificación en vivo](08_VERIFICACION_EN_VIVO_3_SERVIDORES.md) · [Arquitectura](04_ARQUITECTURA_GENERAL.md)

**Fuente principal:** tabla "Datos para tener a mano en la verificación" del Guion del diagrama (17-nov) + diagrama `C4_LANgustino_Azure_FINAL.drawio`. Comando para comprobarlo en vivo: ver final del archivo.

## 1. Máquinas virtuales (10)

| VM | Rol | IP privada | IP pública | Región · VNet · Subred | Tamaño | vCPU / RAM / Disco | SO | Responsable |
|---|---|---|---|---|---|---|---|---|
| SRVD1DEVVPN001 | OpenVPN + PAM (AD vía sssd + TOTP), NAT | 10.1.110.10 | 52.154.177.199 (pip-vpn) | Central US · vnet-langustino-cu · snet-dmz 10.1.110.0/27 | B2pts_v2 | 2 / 1 GiB / 32 GB | Ubuntu 22.04 ARM64 | Santiago Escobar |
| SRVD1DEVPXY001 | Proxy de salida Squid :3128 | 10.1.100.40 | pip-proxy-salida (salida) | Central US · snet-proxy 10.1.100.32/27 | B2pts_v2 | 2 / 1 GiB / 32 GB | Ubuntu 22.04 ARM64 | Equipo de red (ver nota) |
| SRVD1DEVDKR001 | Host Docker (12 contenedores) | 10.1.100.41 | 172.212.220.169 (pip-lbr-dkr001, solo 80/443) | Central US · snet-proxy 10.1.100.32/27 | B2pls_v2 | 2 / 4 GiB / 64 GB | Ubuntu 22.04(.5) ARM64 | Compartido |
| SRVD1DEVBAS001 | Bastión SSH, sin IP pública | 10.1.120.10 | — | North Central US · vnet-langustino-nc · snet-gestion 10.1.120.0/27 | B2ats_v2 | 2 / 1 GiB / 32 GB | Ubuntu 22.04 x64 | **Esteban** |
| SRVD1DEVERP001 | ERP Odoo 17 :8069 | 10.1.100.70 | — | North Central US · snet-erp 10.1.100.64/27 | B2als_v2 | 2 / 4 GiB / 32 GB | Ubuntu 22.04 x64 | Daniel |
| SRVD1DEVSQL001 | PostgreSQL 14 :5432 (bases langustino, lms, cms, msa) | 10.1.100.71 | — | North Central US · snet-erp 10.1.100.64/27 | B2als_v2 | 2 / 4 GiB / 32 GB | Ubuntu 22.04(.5) x64 | Daniel |
| SRVD1DEVAD001 | AD DS + DNS, `langustino.local` | 10.1.100.100 | — | Chile Central · vnet-langustino-r3 · snet-servicios 10.1.100.96/27 | B2als_v2 | 2 / 4 GiB / 127 GB | Windows Server 2022 | Nicolás |
| SRVD1DEVSIE001 | SIEM Wazuh Manager 4.14.10 | 10.1.100.101 | — | Chile Central · snet-servicios | B2als_v2 | 2 / 4 GiB / 32 GB | Ubuntu 22.04 x64 | **Esteban** |
| SRVD1DEVDKR002 | Host Docker (9 contenedores de rol) | 10.1.100.102 | — | Chile Central · snet-servicios | B2as_v2 | 2 / 8 GiB / 64 GB | Ubuntu 22.04(.5) x64 | Compartido |
| SRVD1DEVVEN001 | Vendedor remoto unido a `langustino.local` | 192.168.50.4 | 20.226.80.153 (solo RDP desde IP del admin) | Brazil South · vnet-langustino-vnd 192.168.50.0/24 · snet-vendor 192.168.50.0/27 · **sin peering** | D2s_v4 | 2 / 8 GiB / 127 GB | Windows Server 2022 | Santiago Escobar |

Discos: **Standard HDD (Standard_LRS)** en todas las VM (nota del diagrama). Clientes VPN: **10.8.0.0/24**, NAT a 10.1.110.10.

> Nota PXY001 vs PRX001 ⚠️: existen **dos** Squid. **PXY001** (VM 10.1.100.40) = proxy de salida a Internet de las subredes privadas. **SRVD1DEVPRX001** (contenedor en DKR001, 10.1.100.41:3128) = **filtro de navegación con 16 dominios bloqueados**, el que usan las pruebas 4 y 8 según el consolidado del 17-nov y las pruebas de Pulido. Ver [REFERENCIAS/CAMBIOS_RECIENTES.md](REFERENCIAS/CAMBIOS_RECIENTES.md).

## 2. Contenedores en DKR001 (10.1.100.41) — 12 de rol

| Contenedor | Software | Puerto | Exposición | Responsable | Estado |
|---|---|---|---|---|---|
| SRVD1DEVLBR001 | nginx 1.27, proxy inverso + TLS comodín | TCP 80/443 | **Público** (pip-lbr-dkr001) | Daniel | ✅ (pruebas 7, 9) |
| SRVD1DEVLMS001 | Moodle 4.5 | TCP 8082 | Público vía LBR001 | Daniel | ✅ prueba 9 |
| SRVD1DEVPRX001 | Squid filtro, 16 dominios bloqueados | TCP 3128 | Interno (ACL 10.1.0.0/16) | Pulido | 🟠 desde DKR001 ✅ |
| SRVD1DEVDNS001 | BIND9 interno, reenvía a 168.63.129.16 | TCP/UDP 53 | Interno | Pulido | ✅ zona y recursión |
| SRVD1DEVDNS003 | BIND9 autoritativo (laboratorio) | TCP/UDP 5353 | Interno | Pulido | ✅ en laboratorio |
| SRVD1DEVDHC001 | Kea DHCPv4 | UDP 67 en red Docker aislada 172.30.50.0/24 | Aislado | Pulido | ✅ DORA |
| SRVD1DEVNTP001 | Chrony, fuente PHC de Azure | UDP 123 | Interno | **Esteban** | ✅ |
| SRVD1DEVNOC001-prometheus | Prometheus | 9090 (localhost) | Túnel SSH | **Esteban** | ✅ 6 targets UP |
| SRVD1DEVNOC001-grafana | Grafana | 3000 (localhost) | Túnel SSH | **Esteban** | ✅ |
| SRVD1DEVRAD001 | FreeRADIUS + TOTP | UDP 1812 | Interno | Escobar | Probado en Fase 2; no usado por la VPN |
| SRVD1DEVPKI001 | smallstep/step-ca 0.30.2 | TCP 9000 | Interno | Nicolás | ✅ /health con CA raíz |
| SRVD1DEVFIL001 | Samba (recurso `software`) | TCP 445 | Interno | Nicolás | ✅ local; desde DKR002 timeout |
| SRVD1DEVBKP001 | restic/rest-server 0.14.0 | TCP 8000 (127.0.0.1) | Solo local | Nicolás | ✅ |

Además en el host: node_exporter :9100, agente Wazuh (ID 002), Docker Compose por servicio. Cliente de laboratorio DHCP: `langustino-cliente-dhcp` (172.30.50.100).
*(El diagrama agrupa NOC001 como un rol: Prometheus+Grafana; por eso "12 contenedores de rol".)*

## 3. Contenedores en DKR002 (10.1.100.102) — 9 de rol

| Contenedor | Software | Puerto | Responsable | Estado |
|---|---|---|---|---|
| SRVD1DEVPBX001 | Asterisk 18 (PBX) | SIP UDP/TCP 5060 · RTP UDP 10000-10049 | Daniel | ✅ |
| SRVD1DEVIVR001 | Asterisk 18 + Python (IVR), ext. 500 | SIP 5062 · RTP UDP 10050-10099 · lee SQL001 con `ivr_ro` | Daniel | ✅ |
| SRVD1DEVCMS001 | Drupal 10.6 (intranet) | TCP 8083, solo red interna vía LBR001 | Daniel | 🟠 |
| SRVD1DEVMTA001 | Postfix + Dovecot | SMTP/IMAP internos; relay externo bloqueado | Pulido | 🟠 |
| SRVD1DEVWBM001 | Roundcube (webmail) | publicado por HTTPSWBM001 (TLS) — **puerto no confirmado** | Pulido | ⏳ URL |
| SRVD1DEVHTTPSWBM001 | proxy TLS del webmail | **no confirmado** | Pulido | ⏳ |
| SRVD1DEVMSA001 | Redmine 6.1.6 | TCP 8081 → 3000 interno | Nicolás | ✅ ticket #1 |
| SRVD1DEVNVR001 | MediaMTX v1.21.4 | RTSP 127.0.0.1:8554 | **Esteban** | ✅ |
| SRVD1DEVCAM001 | FFmpeg (cámara simulada testsrc2 H.264) | publica a rtsp://127.0.0.1:8554/camara1 | **Esteban** | ✅ |

En el host: node_exporter :9100 y agente Wazuh (ID 003). Hay respaldos detenidos del NVR y contenedores de prueba sin rol, **fuera de la demo** (nota del diagrama). La guía técnica antigua listaba NVR en 8085: **obsoleto**, el real es RTSP 8554 loopback.

## 4. Puertos por servicio (resumen para responder rápido)

| Puerto | Servicio | Dónde |
|---|---|---|
| UDP 1194 | OpenVPN | VPN001 (público) |
| TCP 22 | SSH | Bastión → todos |
| TCP 3389 | RDP | Bastión → AD001; IP admin → VEN001 |
| TCP 80/443 | nginx LBR001 | DKR001 (público) |
| TCP 8069 | Odoo | ERP001 |
| TCP 5432 | PostgreSQL | SQL001 |
| TCP 3128 | Squid | PRX001 (DKR001) · PXY001 (.40) |
| TCP/UDP 53 | DNS001 · AD DNS | DKR001 · AD001 |
| TCP/UDP 5353 | DNS003 | DKR001 |
| UDP 123 | NTP | DKR001 |
| TCP 9090 / 3000 | Prometheus / Grafana (localhost) | DKR001 |
| TCP 9100 | node_exporter | BAS001, DKR001, DKR002, SIE001 |
| TCP 9182 | windows_exporter 0.32.1 | AD001 (firewall solo 10.1.100.41) |
| TCP 1514 / 1515 | Wazuh eventos / enrolamiento | SIE001 |
| TCP 9000 | step-ca | DKR001 |
| TCP 445 | Samba | DKR001 |
| TCP 8000 (local) | Restic REST | DKR001 |
| UDP 1812 | FreeRADIUS | DKR001 |
| TCP 8081 | Redmine | DKR002 |
| TCP 8082 / 8083 | Moodle / Drupal (detrás de LBR001) | DKR001 / DKR002 |
| SIP 5060 / 5062 · RTP 10000-10099 | PBX / IVR | DKR002 |
| RTSP 8554 (127.0.0.1) | MediaMTX | DKR002 |
| TCP 88, 389, 636, 464, 3268/3269, 135, 49152-65535 | AD | AD001 |

## 5. Nombres DNS

| Nombre | Resuelve a | Por quién |
|---|---|---|
| `langustino.local` | AD001 10.1.100.100 | DNS de AD |
| `lms/erp/cms.langustino-lab.duckdns.org` (público) | 172.212.220.169 | DuckDNS |
| `erp`/`cms` por VPN | 10.1.100.41 | archivo `hosts` (o DNS001) |
| `mail.langustino-lab.duckdns.org` | 10.1.100.102 | DNS001 (solo identifica DKR002; **no** prueba el webmail) |

## 6. Verificar este inventario en vivo

```bash
# [CLOUD SHELL]  🟢
az vm list -g rg-langustino-dev -d --query "[].{VM:name,Region:location,Tamano:hardwareProfile.vmSize,IP:privateIps,Publica:publicIps}" -o table
```
```bash
# [DKR001] o [DKR002]  🟢
sudo docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}"
nproc; free -h; df -h /; lsb_release -d; hostname -I
```
