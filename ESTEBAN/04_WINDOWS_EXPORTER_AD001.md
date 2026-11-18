# 04 · Windows Exporter en AD001

[⬅ Índice Esteban](00_INDICE_ESTEBAN.md) · [AD Nicolás](../NICOLAS/01_ACTIVE_DIRECTORY.md)

**Quién hizo qué:** Nicolás instaló Windows Exporter **0.32.1** en AD001 (servicio Running, `/metrics` HTTP 200 en localhost:9182, firewall limitado a **origen 10.1.100.41**, MSI eliminado tras instalar). Escobar creó la regla NSG **290** (TCP 9182 10.1.100.41 → 10.1.100.100). Esteban configuró el job `ad001` en Prometheus y el dashboard en Grafana.

> ⚠️ Documentos de Nicolás y la guía técnica decían "exporter no instalado": **obsoleto**. La guía de Esteban del 18-nov (más reciente, con evidencia `up=1`) prevalece.

---

### PREGUNTA W1 · ¿Cómo se demuestra que AD001 está monitoreado?
**🗣️ ORAL:** "El exportador corre en AD001 puerto 9182, solo aceptado desde DKR001; Prometheus lo consulta con el job ad001 y devuelve 1, y Grafana grafica CPU, RAM, disco y red del controlador de dominio."

**👤** Esteban (Prometheus/Grafana) + Nicolás (instalación).

**🖥️ / ⌨️ (🟢):**
```bash
# [DKR001]  ssh dkr1
curl -sG --data-urlencode 'query=up{job="ad001"}' http://127.0.0.1:9090/api/v1/query
```
```powershell
# [AD001/POWERSHELL] (solo con RDP autorizado)
(Invoke-WebRequest http://localhost:9182/metrics -UseBasicParsing).StatusCode
Get-Service windows_exporter      # ◇ nombre de servicio estándar del MSI
```

**✅** `10.1.100.100:9182` valor `"1"` · `200` · `Running`.

**📌** ✅ 18-nov.

### PREGUNTA W2 · ¿Por qué el firewall solo permite a DKR001?
"Mínimo privilegio en dos capas: NSG 290 y firewall de Windows; las métricas revelan información del servidor."

### PREGUNTA W3 · ¿Cómo instalaron algo en un AD sin Internet?
"Con un MSI transferido de forma autorizada por Nicolás; no se abrió Internet al AD. El MSI se eliminó después." (Detalle del canal de transferencia: no documentado → no inventar.)
