# 05 · Matriz de aprovisionamiento

[⬅ Índice Santiago](00_INDICE_SANTIAGO.md) · [Inventario completo](../03_INVENTARIO_SERVIDORES_IP_PUERTOS.md)

| VM | Región | Tamaño Azure | vCPU | RAM | Disco (Standard_LRS) | SO / arquitectura |
|---|---|---|---|---|---|---|
| SRVD1DEVVPN001 | Central US | B2pts_v2 | 2 | 1 GiB | 32 GB | Ubuntu 22.04 ARM64 |
| SRVD1DEVPXY001 | Central US | B2pts_v2 | 2 | 1 GiB | 32 GB | Ubuntu 22.04 ARM64 |
| SRVD1DEVDKR001 | Central US | B2pls_v2 | 2 | 4 GiB | 64 GB | Ubuntu 22.04 ARM64 |
| SRVD1DEVBAS001 | North Central US | B2ats_v2 | 2 | 1 GiB | 32 GB | Ubuntu 22.04 x64 |
| SRVD1DEVERP001 | North Central US | B2als_v2 | 2 | 4 GiB | 32 GB | Ubuntu 22.04 x64 |
| SRVD1DEVSQL001 | North Central US | B2als_v2 | 2 | 4 GiB | 32 GB | Ubuntu 22.04 x64 |
| SRVD1DEVAD001 | Chile Central | B2als_v2 | 2 | 4 GiB | 127 GB | Windows Server 2022 |
| SRVD1DEVSIE001 | Chile Central | B2als_v2 | 2 | 4 GiB | 32 GB | Ubuntu 22.04 x64 |
| SRVD1DEVDKR002 | Chile Central | B2as_v2 | 2 | 8 GiB | 64 GB | Ubuntu 22.04 x64 |
| SRVD1DEVVEN001 | Brazil South | D2s_v4 | 2 | 8 GiB | 127 GB | Windows Server 2022 |

**Estado de la matriz oficial:** "DOC-01 Matrices C1-C5 y aprovisionamiento final — pendiente de consolidar equipo" (guía Esteban). Esta tabla sale del guion del diagrama (17-nov). Si la matriz de la propuesta difiere, la regla del criterio 1 nivel 4 exige que coincidan: **revisar antes de presentar**.

### PREGUNTA MA1 · ¿Por qué tamaños ARM (B2pts/B2pls) en Central US?
Respuesta defendible: "Por costo/cuota: las series ARM son más baratas para cargas Linux; Windows y algunos paquetes requieren x64, por eso AD, SIEM y otros son x64." *(Razonamiento; la decisión exacta no está documentada.)*

### PREGUNTA MA2 · ¿Por qué el bastión tiene solo 1 GiB?
"Solo hace de salto SSH y reenvío de túneles; no ejecuta servicios pesados."
