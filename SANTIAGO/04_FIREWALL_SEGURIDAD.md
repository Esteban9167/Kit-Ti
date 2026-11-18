# 04 · Firewall y controles de seguridad por zona

[⬅ Índice Santiago](00_INDICE_SANTIAGO.md) · [Seguridad (transversal)](../PREGUNTAS_TRANSVERSALES/01_SEGURIDAD.md)

## IP públicas (las únicas)
| IP pública | Recurso | Puertos | Para |
|---|---|---|---|
| 52.154.177.199 (pip-vpn) | VPN001 | UDP 1194 | Empleados/vendedores/administradores |
| 172.212.220.169 (pip-lbr-dkr001) | LBR001 en DKR001 | TCP 80/443 | LMS público; ERP/CMS → 403 desde fuera |
| pip-proxy-salida | PXY001 | salida | Navegación saliente de subredes privadas |
| 20.226.80.153 | VEN001 | TCP 3389 solo desde IP del admin | Administración de la VM del vendedor |

## Controles por zona
| Zona | Controles |
|---|---|
| Expuesta | Solo 1194/UDP y 80-443/TCP; VPN con AD + TOTP; nginx allow/deny; TLS Let's Encrypt |
| Interna | Sin IP pública; regla 4000; reglas IP→IP:puerto; administración solo desde bastión (22/3389); exporters solo desde 10.1.100.41; Grafana/Prometheus/Restic en localhost |
| Gestión | Bastión: llave pública, sin root, sin contraseña, AllowUsers, `GatewayPorts no` |
| LAN | Sin peering; RDP a VEN001 restringido a una IP; acceso corporativo solo por VPN |
| Host | Firewall Windows en AD001 limita 9182 a DKR001 |

## Acciones 🔴 de preparación (Escobar, antes; NUNCA en vivo)
```bash
# [CLOUD SHELL] regla 129 (filtro de navegación desde la VPN)
az network nsg rule create -g rg-langustino-dev --nsg-name nsg-langustino-cu -n Allow-VPN-a-PRX001-3128 --priority 129 --access Allow --protocol Tcp --direction Inbound --source-address-prefixes 10.1.110.10 --destination-address-prefixes 10.1.100.41 --destination-port-ranges 3128 --source-port-ranges "*"
# Agregar IP de otro integrante a la VM del vendedor
az network nsg rule update -g rg-langustino-dev --nsg-name nsg-langustino-vnd -n Allow-RDP-Santiago --source-address-prefixes IP_DE_SANTIAGO/32 IP_DEL_INTEGRANTE/32
# Evitar que VEN001 se apague
az vm auto-shutdown -g rg-langustino-dev -n SRVD1DEVVEN001 --time 0400 --location brazilsouth
```
Cada integrante obtiene su IP pública con `curl.exe ifconfig.me` (🟢).
