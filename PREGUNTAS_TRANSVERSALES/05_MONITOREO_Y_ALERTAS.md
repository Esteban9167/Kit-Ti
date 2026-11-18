# 05 · Monitoreo y alertas

[⬅ README](../README.md) · [NOC](../ESTEBAN/02_NOC_PROMETHEUS.md) · [Grafana](../ESTEBAN/03_GRAFANA.md) · [Wazuh](../ESTEBAN/06_SIEM_WAZUH.md)

| Pieza | Dónde | Integrantes |
|---|---|---|
| Prometheus + Grafana | DKR001 | Esteban |
| node_exporter | BAS001, DKR001, DKR002, SIE001 | Esteban |
| windows_exporter | AD001 | Nicolás (instaló) + Esteban |
| Reglas NSG 165/250/260/290 | Azure | Escobar |
| Wazuh | SIE001 + agentes | Esteban |

### PREGUNTA MON1 · ¿Cómo distingue Grafana una caída del servidor de una falla del exportador?
Ver [ESTEBAN/02 N3](../ESTEBAN/02_NOC_PROMETHEUS.md). Resumen: `up=0` + host responde a SSH → exportador/puerto; nada responde → servidor/red.

### PREGUNTA MON2 · ¿Hay alertas?
Regla Grafana "LANgustino - CPU Alta DKR001" (>80 % 1 min) configurada; **sin disparo probado**; sin canal de notificación evidenciado.

### PREGUNTA MON3 · ¿Qué no se monitorea?
VPN001, PXY001, ERP001, SQL001 no aparecen como targets (los 6 targets son Prometheus, DKR001, BAS001, DKR002, SIE001, AD001). Pendiente agregarlos.
