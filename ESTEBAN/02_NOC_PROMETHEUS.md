# 02 · NOC — Prometheus (REQ-ADM-05)

[⬅ Índice Esteban](00_INDICE_ESTEBAN.md) · [Grafana](03_GRAFANA.md) · [Target DOWN](../ESCENARIOS_DE_FALLO/05_PROMETHEUS_TARGET_DOWN.md)

**Ficha:** contenedor `SRVD1DEVNOC001-prometheus` en DKR001 · escucha **127.0.0.1:9090** · `scrape_interval 15s` · restart `unless-stopped`. ⚠️ Prometheus estuvo `Exited (0)` y se inició manualmente: **verificar antes de la demo**. "Persistencia configurada" ≠ prueba de reinicio completo del host.

| Target | Dirección | Exporter | Regla NSG | Último estado |
|---|---|---|---|---|
| prometheus | prometheus:9090 | propio | local | UP=1 |
| DKR001 | node-exporter:9100 | node_exporter | local | UP=1 |
| Bastión | 10.1.120.10:9100 | node_exporter | nc 165 | UP=1 |
| DKR002 | 10.1.100.102:9100 | node_exporter | r3 250 | UP=1 |
| SIEM001 | 10.1.100.101:9100 | node_exporter | r3 260 | UP=1 |
| AD001 (job `ad001`) | 10.1.100.100:9182 | windows_exporter 0.32.1 | r3 290 | UP=1 |

---

### PREGUNTA N1 · ¿Cómo sabemos que Prometheus monitorea los servidores?

**🗣️ ORAL:** "Consulto la métrica `up` en Prometheus: devuelve seis series con valor 1, una por objetivo: Prometheus, DKR001, bastión, DKR002, SIEM001 y AD001. Uno significa que el último scrape fue exitoso."

**👤** Esteban · **🖥️** `[DKR001]` 10.1.100.41 · **🔑** `ssh dkr1`

**⌨️ (🟢):**
```bash
curl -sG --data-urlencode 'query=up' http://127.0.0.1:9090/api/v1/query | python3 -m json.tool
```

**✅ RESULTADO:** 6 elementos en `result`, cada uno con `"value": [<timestamp>, "1"]`.

**➕** Captura NOC-09; Grafana.

**⚠️ SI FALLA:** `Connection refused` → contenedor detenido: `sudo docker ps -a --filter name=SRVD1DEVNOC001` (🟢). Iniciarlo es 🟡 (`sudo docker start SRVD1DEVNOC001-prometheus`) — solo si no hay alternativa y avisando.

**📌** ✅ 18-nov · **📚** guía Esteban §4.

---

### PREGUNTA N2 · ¿Cómo sabemos que Prometheus monitorea Active Directory?

**🗣️ ORAL:** "AD001 tiene Windows Exporter 0.32.1 en el puerto 9182, con firewall que solo acepta a DKR001. Prometheus lo consulta con el job `ad001` y devuelve 1."

**🖥️** `[DKR001]` · **🔑** `ssh dkr1`

**⌨️ (🟢):**
```bash
curl -sG --data-urlencode 'query=up{job="ad001"}' http://127.0.0.1:9090/api/v1/query
```

**✅** `"instance":"10.1.100.100:9182"` … `"value":[…,"1"]`.

**➕** Dashboard Windows Exporter en Grafana (CPU, RAM, disco, red; CPU 2,47 % observado).

**📌** ✅ 18-nov.

---

### PREGUNTA N3 · ¿Distingue Grafana una caída del servidor de una falla del exportador?

**🗣️ ORAL:** "`up=0` solo dice que el scrape falló. Para distinguir: si el servidor responde a SSH o ping pero `up=0`, es el exportador o el puerto; si no responde a nada, es el servidor o la red. Por eso contrasto `up` con conectividad."

**⌨️ (🟢):**
```bash
# [DKR001]
curl -sG --data-urlencode 'query=up==0' http://127.0.0.1:9090/api/v1/query
curl -s -m 3 http://10.1.100.102:9100/metrics | head -n 3     # ◇ exportador directo
# [BASTION]
timeout 3 bash -c 'echo > /dev/tcp/10.1.100.102/22' && echo "host vivo"
```

**📌** Razonamiento; comandos ◇ sugeridos.

---

### PREGUNTA N4 · ¿Qué demuestra UP=1 y qué NO?
"Demuestra que en ese instante Prometheus obtuvo métricas. **No** garantiza uptime histórico ni que la aplicación del servidor funcione."

### PREGUNTA N5 · ¿Por qué Prometheus solo en localhost?
"Para no exponer un panel administrativo; se accede con túnel SSH por el bastión. Las NSG solo abren 9100/9182 desde 10.1.100.41 hacia los objetivos."

### Comprobar política de reinicio (🟢)
```bash
sudo docker inspect --format '{{.Name}}: {{.HostConfig.RestartPolicy.Name}}' SRVD1DEVNOC001-prometheus SRVD1DEVNOC001-grafana
```
Resultado: `/SRVD1DEVNOC001-prometheus: unless-stopped` · `/SRVD1DEVNOC001-grafana: unless-stopped`.
