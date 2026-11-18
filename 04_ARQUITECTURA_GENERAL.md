# 04 · Arquitectura general

[⬅ README](README.md) · [Inventario](03_INVENTARIO_SERVIDORES_IP_PUERTOS.md) · [Flujos](PREGUNTAS_TRANSVERSALES/12_FLUJOS_ENTRE_SISTEMAS.md) · [Guion del diagrama](10_SUSTENTACION_DIAGRAMA.md)

## 1. Vista de alto nivel

```text
                                INTERNET
          ┌───────────────────────┼─────────────────────────────┐
   UDP 1194 (pip-vpn 52.154.177.199)          TCP 80/443 (pip-lbr-dkr001 172.212.220.169)
          ▼                                                     ▼
 ┌─ CENTRAL US · vnet-langustino-cu ─────────────────────────────────────────────┐
 │ snet-dmz 10.1.110.0/27 (EXPUESTA)     snet-proxy 10.1.100.32/27 (INTERNA, LBR001 expuesto) │
 │  VPN001 10.1.110.10                    PXY001 10.1.100.40 (Squid salida)      │
 │  OpenVPN+PAM(sssd AD)+TOTP             DKR001 10.1.100.41: LBR001, LMS001, PRX001, DNS001, │
 │  clientes 10.8.0.0/24 → NAT            DNS003, DHC001, NTP001, NOC001, RAD001, PKI001,  │
 │                                        FIL001, BKP001                          │
 └───────────────┬──────────────────── peering en malla ────────────────────────┘
                 │
 ┌─ NORTH CENTRAL US · vnet-langustino-nc ─────────┐   ┌─ CHILE CENTRAL · vnet-langustino-r3 ──────┐
 │ snet-gestion 10.1.120.0/27: BAS001 10.1.120.10  │   │ snet-servicios 10.1.100.96/27              │
 │ snet-erp 10.1.100.64/27: ERP001 .70 → SQL001 .71│◄─►│ AD001 .100 · SIE001 .101 · DKR002 .102     │
 └─────────────────────────────────────────────────┘   │ DKR002: PBX001, IVR001, CMS001, MTA001,    │
                                                        │ WBM001, HTTPSWBM001, MSA001, NVR001, CAM001│
                                                        └────────────────────────────────────────────┘
 ┌─ BRAZIL SOUTH · vnet-langustino-vnd 192.168.50.0/24 (ZONA LAN, SIN PEERING) ─┐
 │ VEN001 192.168.50.4 · pública 20.226.80.153 (RDP solo desde IP admin)         │
 │ Unido a langustino.local; llega a LANgustino SOLO por la VPN                  │
 └───────────────────────────────────────────────────────────────────────────────┘
```

## 2. Las tres zonas que exige la rúbrica (colores del diagrama)

| Zona | Color | Qué contiene | Control |
|---|---|---|---|
| Servidores expuestos | Rojo | snet-dmz (VPN001) + LBR001 en DKR001 | Solo UDP 1194 y TCP 80/443 desde Internet |
| Servidores internos | Verde | snet-proxy, snet-erp, snet-gestion, snet-servicios | Sin IP pública (salvo excepción DKR001/LBR); regla 4000 Deny VNet→VNet |
| LAN (usuarios) | Amarillo | vnet-langustino-vnd (VEN001) | Sin peering: solo entra por VPN |

## 3. Decisiones de arquitectura y su justificación

| Decisión | Por qué | Alternativa descartada | Trade-off reconocido |
|---|---|---|---|
| 3 regiones + peering en malla | Cuota de 6 vCPU por región (Azure for Students) | Una sola región | El peering **no es transitivo**: cada flujo inter-región necesita su regla; más latencia |
| Contenedores de rol único en 2 hosts | Cuota y costo; cada contenedor es "un servidor" con un rol | Una VM por servicio | DKR001/DKR002 son puntos únicos de falla |
| VM propias para ERP, SQL, AD, SIEM | Críticos o pesados; AD DS exige Windows Server | Contenedores | Más vCPU usadas |
| AD en VM Windows | AD DS requiere controlador de dominio Windows | Samba AD | Sin Internet utilizable → instalación de agentes complicada |
| Bastión sin IP pública detrás de VPN | Un solo punto auditable para SSH/RDP | Azure Bastion (costo), SSH público | Si BAS001 cae, no hay administración (salvo Cloud Shell `run-command`) |
| VPN con PAM + sssd + TOTP | Integra con AD y OpenVPN con pocas piezas | RADIUS (RAD001 existe, probado en Fase 2) | PAM aún acepta usuarios locales como respaldo |
| LBR001 único punto público web | Terminación TLS centralizada; allow/deny por IP | Application Gateway (costo) | DKR001 sale a Internet sin pasar por proxy: **excepción declarada** |
| Certificado comodín Let's Encrypt por DNS-01 | CA reconocida, sin exponer servicios para validar | Certificado de PKI interna (no confiable para público) | Renovación cada 90 días (cron diario) |
| PKI interna step-ca | Certificados para servicios internos | Usarla para web pública | Falta distribuir raíz por GPO y LDAPS |
| PostgreSQL central en SQL001 | Datos fuera de los servidores de aplicación; un usuario y una regla 5432 por app | BD dentro de cada contenedor | SQL001 punto único de falla para ERP, LMS, CMS, MSA, IVR |
| NOC (Prometheus+Grafana) en localhost + túnel | No exponer paneles administrativos | Publicarlos en Internet | Requiere túnel para ver |
| Wazuh Manager sin indexer/dashboard | Recursos de la VM (2 vCPU / 4 GiB) | Stack completo | Sin correlación visual; alertas en `alerts.json` |
| DHCP en red Docker aislada | Azure no propaga broadcast DHCP entre VM | DHCP real en VNet | Es demostración de laboratorio |
| NTP interno en DKR001 | Trazabilidad temporal (logs, SIEM, Kerberos) | Cada VM con su propia fuente | Fuente horaria de AD/PDC no verificada |

## 4. Automatización (criterio 4 de la rúbrica: ad hoc → scripts → IaC → contenedores → CI/CD)

| Nivel | ¿Lo tenemos? | Evidencia | Qué NO afirmar |
|---|---|---|---|
| Scripts | ✅ | Scripts de respaldo/restauración (`backup-fil.sh`, `verificar-restauracion.sh`), comandos `az` documentados | — |
| CLI del proveedor | ✅ | `az network nsg rule create/update`, `az vm list`, `az vm run-command`, `az vm auto-shutdown` | — |
| Contenedores | ✅ | 21 contenedores con Docker Compose por servicio, políticas `unless-stopped` | — |
| IaC (Terraform/Bicep) | ⏳ no evidenciado | No hay documento que muestre plantillas IaC | **No decir que hay IaC** |
| CI/CD | ❌ no evidenciado | — | No afirmarlo |

**Frase lista:** "Estamos en el nivel de contenedores: cada servicio liviano tiene su Compose versionable y la red se configuró con la CLI de Azure. El siguiente paso sería llevar VNets, NSG y VM a Bicep o Terraform para redesplegar desde configuración versionada."

## 5. Disponibilidad, RTO y RPO (lo que se puede defender)

- **RPO de 15 min** solo está demostrado para **FIL001 y la base `msa` (Redmine)**: cron cada 15 min, restauración de 58 tablas verificada.
- **ERP:** sin respaldo automatizado confirmado → **RPO/RTO del ERP no demostrados**.
- **RTO:** no medido.
- **Alta disponibilidad:** no hay; entorno DEV con puntos únicos de falla (DKR001, DKR002, SQL001, AD001, VPN001, BAS001). Ver [PREGUNTAS_TRANSVERSALES/09_ALTA_DISPONIBILIDAD.md](PREGUNTAS_TRANSVERSALES/09_ALTA_DISPONIBILIDAD.md).

## 6. Flujos clave del diagrama (caja "Flujos clave de las pruebas")

| Flujo | Recorrido |
|---|---|
| Vendedor | VEN001 → VPN (usuario AD + TOTP) → IP 10.8.0.x → https://erp… → LBR001 → ERP001 → SQL001 |
| Cliente IVR | softphone → VPN → PBX001 (5060) → IVR001 → SQL001 (solo lectura) |
| LMS | Internet → pip-lbr-dkr001 443 → LBR001 → LMS001 → SQL001 |
| Intranet | VPN → LBR001 (403 fuera de 10.0.0.0/16) → CMS001 → SQL001 |
| Administración | VPN → BAS001 → SSH a cada servidor; túnel RDP a AD001 |
| Monitoreo | NOC001 (DKR001) cada 15 s → node_exporter :9100 (BAS001, DKR002, SIE001, DKR001 local) + windows_exporter :9182 (AD001) |
| SIEM | Agentes Wazuh de BAS001, DKR001, DKR002 → SIE001 TCP 1514 (enrolamiento 1515) |
| Tiempo | BAS001, DKR002, SIE001 → NTP001 UDP 123 (reglas 125-127) |
