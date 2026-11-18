# 05 · Prometheus target DOWN

[⬅ Plan B](../07_PLAN_B_SI_ALGO_FALLA.md) · [NOC](../ESTEBAN/02_NOC_PROMETHEUS.md)

`[DKR001]` 🟢:
```bash
curl -sG --data-urlencode 'query=up==0' http://127.0.0.1:9090/api/v1/query
curl -s -m 3 http://IP_DEL_TARGET:9100/metrics | head -n 3     # 9182 para AD001
```
| Resultado | Causa | Qué decir |
|---|---|---|
| curl timeout y host no responde a SSH | VM apagada | Escenario 01 |
| curl timeout pero host responde a SSH | exportador detenido o NSG 9100/9182 | "Falla del exportador, no del servidor" |
| curl OK pero up=0 | Configuración del job | Mostrar captura NOC-09 |
| AD001 down | Servicio windows_exporter o firewall Windows (solo DKR001) | Requiere RDP autorizado |
**NO hacer:** cambiar NSG ni `prometheus.yml` en vivo.
