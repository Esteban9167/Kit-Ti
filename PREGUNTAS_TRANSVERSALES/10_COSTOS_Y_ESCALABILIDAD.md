# 10 · Costos y escalabilidad

[⬅ README](../README.md)

| Decisión de costo | Efecto |
|---|---|
| Azure for Students, 6 vCPU/región | 3 regiones de 3 VM |
| Tamaños B (burstable), ARM en Central US | Bajo costo; créditos de CPU limitados |
| Discos Standard HDD (Standard_LRS) | Barato, menos IOPS |
| 21 contenedores en 2 hosts | Ahorra ~19 VM |
| Sin Azure Firewall / App Gateway / Azure Bastion / VPN Gateway | NSG + nginx + bastión propio + OpenVPN |
| Wazuh sin indexer | Cabe en 4 GiB |
| Auto-apagado de VM | Ahorro; riesgo de apagado en la sustentación (VEN001) |

**Escalar:** más cuota → mover servicios críticos a VM o PaaS; IaC (Bicep/Terraform) para redesplegar; balanceo y réplicas.

**Burstable:** la prueba de CPU al 100 % en el bastión consume créditos de CPU de una VM B; por eso se limita a 60 s con `nice -n 19`. *(Análisis.)*
