# 04 · Grafana sin datos

[⬅ Plan B](../07_PLAN_B_SI_ALGO_FALLA.md) · [Grafana](../ESTEBAN/03_GRAFANA.md)

| Paso | Dónde | Comando / acción 🟢 | Interpretación |
|---|---|---|---|
| 1 | `[MAC]` | ¿Túnel `ssh -N -L 3000:127.0.0.1:3000 dkr1` abierto? | Si no carga Grafana → reabrir túnel |
| 2 | `[DKR001]` | `sudo docker ps --format "table {{.Names}}\t{{.Status}}" \| grep NOC001` | Prometheus `Exited` → panel vacío |
| 3 | `[DKR001]` | `curl -sG --data-urlencode 'query=up' http://127.0.0.1:9090/api/v1/query` | Targets en 0 → escenario 05 |
| 4 | `[NAVEGADOR/GRAFANA]` | Rango de tiempo "Last 5/15 minutes"; variable Job correcta | Rango o variable mal elegidos |
| 5 | Dashboard Windows | Panel *Overview of Server Resources* = **No rows** | Limitación conocida del template, no fallo |
Iniciar Prometheus detenido: `sudo docker start SRVD1DEVNOC001-prometheus` (🟡; solo si es imprescindible y avisando).
