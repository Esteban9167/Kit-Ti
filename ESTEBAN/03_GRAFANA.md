# 03 · Grafana

[⬅ Índice Esteban](00_INDICE_ESTEBAN.md) · [Grafana sin datos](../ESCENARIOS_DE_FALLO/04_GRAFANA_SIN_DATOS.md)

**Ficha:** contenedor `SRVD1DEVNOC001-grafana` en DKR001 · **127.0.0.1:3000** · `unless-stopped` · acceso por túnel.

## Ruta de navegación
1. `[MAC]` `ssh -N -L 3000:127.0.0.1:3000 dkr1` (dejar abierta).
2. `[NAVEGADOR/GRAFANA]` http://localhost:3000 → usuario del gestor.
3. **Dashboards → LANgustino - Monitoreo NOC** → variable Job/host: `bastion`, `dkr001`, `dkr002`, `siem001` → CPU, RAM, disco, red.
4. **Dashboards → Windows Exporter Dashboard 2025 (v0.31+ compatible)** → job `ad001`, host `SRVD1DEVAD001`, instance `10.1.100.100:9182`.
5. **Alerting → Alert rules → "LANgustino - CPU Alta DKR001"** (umbral > 80 %, pendiente 1 min).

---

### PREGUNTA G1 · ¿Qué muestra el monitoreo?
**🗣️ ORAL:** "Cuatro servidores Linux con Node Exporter y el AD con Windows Exporter: CPU, memoria, disco y red, con scrape cada 15 segundos."

**🖥️** `[NAVEGADOR/GRAFANA]` · **✅** paneles con datos para los 4 Linux y AD001 · **📌** ✅ (capturas NOC-08, 10-13).

### PREGUNTA G2 · ¿Probaron la alerta?
**🗣️ ORAL:** "La regla de CPU alta de DKR001 está configurada con umbral 80 % durante un minuto. No hicimos una prueba de disparo, así que no afirmo que se haya activado."

**🖥️** Alerting → Alert rules · **📌** ⏳ disparo pendiente.

### PREGUNTA G3 · ¿Qué problema encontraron con el dashboard de Windows?
**🗣️ ORAL:** "El dashboard importado consultaba `windows_cpu_time_total`, que ya no existe; Windows Exporter 0.32.1 publica `windows_cpu_time_seconds_total`. Reemplazamos la consulta del panel *CPU Usage by Host* y apareció la gráfica."

**Consulta corregida:**
```promql
100 * (1 - avg by (instance) (rate(windows_cpu_time_seconds_total{job="$job",mode="idle"}[5m])))
```

**Nota:** el cuadro *Overview of Server Resources* muestra **No rows** — limitación del template, no ausencia de servicio.

### PREGUNTA G4 · ¿Cómo mostraría el cambio de comportamiento (prueba 6)?
Ver [P06](../PRUEBAS_RECORRIDO/P06_ADMINISTRACION_TI.md) paso 5: carga controlada con `timeout 60` en el bastión y Job = bastion, rango 5 min.

### Consulta PromQL útil para CPU Linux (◇ sugerida, para Explore)
```promql
100 - (avg by (instance) (rate(node_cpu_seconds_total{mode="idle"}[1m])) * 100)
```
