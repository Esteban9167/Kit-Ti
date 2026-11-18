# Santiago Escobar — Índice

[⬅ README](../README.md)

**Componentes (según documentos):** arquitectura Azure, VNets, peering, NSG (reglas 4000 y explícitas), VPN001 (OpenVPN + PAM sssd + TOTP), VM del vendedor VEN001, RAD001 (Fase 2), guion del diagrama, inventario `az vm list`. Pruebas: **2** (principal), 4, 5, 8 (VEN001 y reglas), apoyo en 6.

| Archivo | Tema |
|---|---|
| [01_ARQUITECTURA_AZURE.md](01_ARQUITECTURA_AZURE.md) | Regiones, RG, cuotas |
| [02_VNET_SUBREDES_NSG.md](02_VNET_SUBREDES_NSG.md) | VNets, subredes, reglas NSG transcritas |
| [03_ENRUTAMIENTO_CONECTIVIDAD.md](03_ENRUTAMIENTO_CONECTIVIDAD.md) | Peering, NAT VPN, salida a Internet |
| [04_FIREWALL_SEGURIDAD.md](04_FIREWALL_SEGURIDAD.md) | Controles por zona, IP públicas |
| [05_MATRIZ_APROVISIONAMIENTO.md](05_MATRIZ_APROVISIONAMIENTO.md) | VM, tamaños, discos |
| [06_PREGUNTAS_DIFICILES.md](06_PREGUNTAS_DIFICILES.md) | — |
| [07_GUION_DEMOSTRACION.md](07_GUION_DEMOSTRACION.md) | Prueba 2 + diagrama |
| [08_VPN_OPENVPN_TOTP.md](08_VPN_OPENVPN_TOTP.md) | VPN001 *(adicional)* |
| [09_VM_VENDEDOR_VEN001.md](09_VM_VENDEDOR_VEN001.md) | VEN001 *(adicional)* |

**Tareas 🔴 que solo él hace (antes, no en vivo):** regla 129 (3128 hacia PRX001), regla 300 (webmail), IP de otros integrantes en `nsg-langustino-vnd`, auto-shutdown de VEN001, compartir TOTP de vendedor1.
