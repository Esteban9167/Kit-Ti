# 01 · Arquitectura Azure

[⬅ Índice Santiago](00_INDICE_SANTIAGO.md) · [Arquitectura general](../04_ARQUITECTURA_GENERAL.md)

- **Suscripción:** Azure for Students · **Grupo de recursos:** `rg-langustino-dev`.
- **9 VM corporativas** en 3 regiones + **1 VM vendedor** en Brazil South.
- **Motivo de 3 regiones:** cuota de **6 vCPU por región**; 9 VM × 2 vCPU = 18 vCPU → 3 regiones.

| Región | VNet | Espacio | VM |
|---|---|---|---|
| Central US | vnet-langustino-cu | 10.1.110.0/27 y 10.1.100.32/27 | VPN001, PXY001, DKR001 (6 vCPU) |
| North Central US | vnet-langustino-nc | 10.1.120.0/27 y 10.1.100.64/27 | BAS001, ERP001, SQL001 (6 vCPU) |
| Chile Central | vnet-langustino-r3 | 10.1.100.96/27 | AD001, SIE001, DKR002 (6 vCPU) |
| Brazil South | vnet-langustino-vnd | 192.168.50.0/24 | VEN001 (sin peering) |

### PREGUNTA AZ1 · ¿Por qué esta arquitectura?
**🗣️ ORAL:** "Por cuota: 6 vCPU por región nos obligó a tres regiones de 3 VM de 2 vCPU. Las unimos con peering en malla porque el peering no es transitivo. Los servicios ligeros van en contenedores para caber en la cuota; lo crítico en VM propias."

**🖥️** `[AZURE PORTAL]` rg-langustino-dev → Recursos · `[CLOUD SHELL]` `az vm list …` (🟢).

### PREGUNTA AZ2 · ¿Zonas de disponibilidad?
No hay evidencia de despliegue en zonas de disponibilidad; las VM son únicas por rol. No afirmar AZ.

### PREGUNTA AZ3 · ¿IaC?
No evidenciado. Configuración con **CLI de Azure** (`az network nsg rule create`, `az vm …`). Ver [04_ARQUITECTURA_GENERAL.md](../04_ARQUITECTURA_GENERAL.md) §4.
