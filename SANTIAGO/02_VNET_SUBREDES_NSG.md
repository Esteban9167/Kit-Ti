# 02 · VNets, subredes y NSG

[⬅ Índice Santiago](00_INDICE_SANTIAGO.md) · [Seguridad](../PREGUNTAS_TRANSVERSALES/01_SEGURIDAD.md)

## Subredes

| Subred | CIDR | Zona | VM |
|---|---|---|---|
| snet-dmz | 10.1.110.0/27 | Expuesta | VPN001 .10 |
| snet-proxy | 10.1.100.32/27 | Interna (LBR001 expuesto) | PXY001 .40, DKR001 .41 |
| snet-gestion | 10.1.120.0/27 | Interna de gestión | BAS001 .10 |
| snet-erp | 10.1.100.64/27 | Interna | ERP001 .70, SQL001 .71 |
| snet-servicios | 10.1.100.96/27 | Interna | AD001 .100, SIE001 .101, DKR002 .102 |
| snet-vendor | 192.168.50.0/27 (VNet 192.168.50.0/24) | LAN | VEN001 .4 |
| (VPN clientes) | 10.8.0.0/24 | Túnel, NAT a 10.1.110.10 | — |

## Lógica de las NSG
- **4000 Deny VirtualNetwork → VirtualNetwork** en cada NSG; solo flujos explícitos **IP/32 → IP/32:puerto**, en el **NSG del destino**.
- **180 Allow ICMP VirtualNetwork** (diagnóstico). *Un ping no prueba un puerto TCP.*

## Reglas transcritas del diagrama (`C4_LANgustino_Azure_FINAL.drawio`)
> ⚠️ Transcripción del PNG del diagrama; ante duda, la fuente es el portal: `az network nsg rule list -g rg-langustino-dev --nsg-name <nsg> -o table` (🟢).

### nsg-langustino-cu (snet-dmz y snet-proxy)
| Prio | Regla |
|---|---|
| 100 | Allow UDP 1194 Internet → 10.1.110.10 (VPN001) |
| 105 | Allow TCP 80, 443 Internet → 10.1.100.41 (LBR001) |
| 115 | Allow TCP 80, 443, 8082 10.1.110.10 (VPN) → 10.1.100.41 |
| 120 | Allow TCP 3128 10.1.0.0/16 → 10.1.100.40 (PXY001) |
| 125/126/127 | Allow UDP 123 10.1.120.10, 10.1.100.102, 10.1.100.101 → 10.1.100.41 (NTP001) |
| 128 | Allow TCP/UDP 53 10.1.100.102 y 10.1.100.100 → 10.1.100.41 (DNS001) |
| 160 / 170 | Allow TCP 22 10.1.110.10 → 10.1.120.10 · TCP 22 10.1.120.10 → 10.1.0.0/16 |
| 180 | Allow ICMP VirtualNetwork |
| 4000 | Deny VirtualNetwork → VirtualNetwork |
| **129 (propuesta 17-nov)** | Allow TCP 3128 10.1.110.10 → 10.1.100.41 (PRX001) — **no aparece en el diagrama; creación sin confirmar** |

*(El cuadro del diagrama también lista 130/140/150 hacia 10.1.100.70/71; esas reglas aplican a destinos de vnet-nc y figuran en nsg-langustino-nc.)*

### nsg-langustino-nc (snet-gestion y snet-erp)
| Prio | Regla |
|---|---|
| 100 | Allow UDP 1194 Internet → 10.1.110.10 |
| 120 | Allow TCP 3128 10.1.0.0/16 → 10.1.100.40 |
| 130 | Allow TCP 8069 10.1.110.10 (VPN) → 10.1.100.70 |
| 135 | Allow TCP 8069 10.1.100.41 (LBR001) → 10.1.100.70 |
| 140 | Allow TCP 8069 10.1.120.0/27 → 10.1.100.70 |
| 150 | Allow TCP 5432 10.1.100.70 → 10.1.100.71 |
| 155 | Allow TCP 5432 10.1.100.102 (DKR002) → 10.1.100.71 |
| 158 | Allow TCP 5432 10.1.100.41 (LMS001) → 10.1.100.71 |
| 160 | Allow TCP 22 10.1.110.10 → 10.1.120.10 |
| 165 | Allow TCP 9100 10.1.100.41 (Prometheus) → 10.1.120.10 |
| 170 | Allow TCP 22 10.1.120.10 → 10.1.0.0/16 |
| 180 | Allow ICMP VirtualNetwork |
| 4000 | Deny VirtualNetwork → VirtualNetwork |

### nsg-langustino-r3 (snet-servicios)
| Prio | Regla |
|---|---|
| 170 | Allow TCP 22 10.1.120.10 → 10.1.0.0/16 |
| 175 | Allow TCP 3389 10.1.120.10 → 10.1.100.100 (RDP al AD) |
| 180 | Allow ICMP VirtualNetwork |
| 190 | Allow TCP 1514, 1515 10.1.0.0/16 → 10.1.100.101 (Wazuh) |
| 200 | Allow TCP 8082, 8083 10.1.110.10 (VPN) → 10.1.100.102 |
| 210 | Allow TCP 8082, 8083 10.1.100.41 (LBR001) → 10.1.100.102 |
| 220 / 230 / 240 | Allow UDP 5060 · UDP 10000-10099 · TCP 5060: 10.1.110.10 (VPN) → 10.1.100.102 (PBX/IVR) |
| 250 | Allow TCP 9100 10.1.100.41 → 10.1.100.102 |
| 260 | Allow TCP 9100 10.1.100.41 → 10.1.100.101 |
| 270 | Allow TCP 53, 88, 135, 389, 445, 464, 636, 3268, 3269, 49152-65535 10.1.0.0/16 → 10.1.100.100 (AD) |
| 280 | Allow UDP 53, 88, 123, 389, 464 10.1.0.0/16 → 10.1.100.100 (AD) |
| 290 | Allow TCP 9182 10.1.100.41 → 10.1.100.100 (windows_exporter) |
| 4000 | Deny VirtualNetwork → VirtualNetwork |
| **300 (pendiente)** | Allow TCP <puerto webmail> 10.1.110.10 → 10.1.100.102 — **por crear cuando Pulido confirme el puerto** |

### nsg-langustino-vnd
| Prio | Regla |
|---|---|
| 100 | Allow TCP 3389 solo desde la IP del administrador (regla `Allow-RDP-Santiago`) |
| — | Sin peering: solo llega a LANgustino por la VPN |

### PREGUNTA NSG1 · ¿Qué reglas permiten los flujos necesarios? (ejemplo IVR)
**🗣️ ORAL:** "Para el IVR, en el NSG de Chile Central abrimos desde la IP de la VPN 10.1.110.10 hacia DKR002: UDP y TCP 5060 para SIP y UDP 10000-10099 para RTP, reglas 220 a 240. Y DKR002 hacia SQL001 por 5432, regla 155 en el NSG de North Central."

**🖥️** `[AZURE PORTAL]` → nsg-langustino-r3 → Reglas de seguridad de entrada.

### PREGUNTA NSG2 · ¿Por qué la regla en el NSG del destino?
"El NSG asociado a la subred del destino evalúa el tráfico entrante; así cada región controla quién entra a sus servidores, independientemente del origen."

### PREGUNTA NSG3 · ¿Por qué SMB DKR002→DKR001 da timeout?
"No hay regla 445 desde 10.1.100.102 hacia 10.1.100.41 en nsg-langustino-cu: la regla 4000 lo niega. Es coherente con el diseño; si se necesita, se abre solo esa IP→IP:445."
*(Explicación consistente con la tabla del diagrama; no hay evidencia de diagnóstico formal.)*
