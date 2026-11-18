# 06 · SIEM — Wazuh Manager

[⬅ Índice Esteban](00_INDICE_ESTEBAN.md) · [Wazuh sin agentes](../ESCENARIOS_DE_FALLO/06_WAZUH_SIN_AGENTES.md)

**Ficha:** SRVD1DEVSIE001 · 10.1.100.101 · Chile Central · B2als_v2 2 vCPU / 4 GiB / 32 GB · Ubuntu 22.04 x64 · **Wazuh Manager 4.14.10** · TCP **1514** (eventos) / **1515** (enrolamiento) · NSG r3 190 (desde 10.1.0.0/16).

**Agentes:** 000 SIEM001 (local) · 001 Bastión · 002 DKR001 · 003 DKR002.

**No hay** Wazuh Indexer ni Dashboard (recursos). Alertas en `/var/ossec/logs/alerts/alerts.json`.

| Fuente | Agente | Alertas en 30 min (18-nov 09:09 UTC) |
|---|---|---|
| Bastión | 001 | 29 |
| DKR001 | 002 | 5 |
| DKR002 | 003 | 18 |
| SIEM001 | 000 | 44 |
| **Total** | | **96** |

Histórico: `alerts.json` tenía 694 líneas al último conteo (685 alertas en una consulta previa; crece). Tipos reales: sudo, PAM, SSH (regla 5710 nivel 5 registrada).

---

### PREGUNTA S1 · ¿Cómo demuestran ingesta de múltiples fuentes?
**🗣️ ORAL:** "Wazuh tiene cuatro agentes activos: bastión, DKR001, DKR002 y el propio SIEM. En una ventana de 30 minutos registró 96 alertas repartidas entre las cuatro fuentes: cambios de privilegio con sudo, autenticaciones PAM y eventos SSH."

**👤** Esteban · **🖥️** `[SIEM001]` · **🔑** `ssh siem`

**⌨️ (🟢):**
```bash
sudo /var/ossec/bin/agent_control -l
sudo grep -o '"agent":{"id":"[0-9]*","name":"[^"]*"' /var/ossec/logs/alerts/alerts.json | sort | uniq -c
```

**✅** 000 `Active/Local`, 001-003 `Active` · conteo con 4 nombres distintos.

**➕** Script de ventana de 30 min: ver [06_COMANDOS_MAESTROS.md](../06_COMANDOS_MAESTROS.md) sección SIEM001.

**⚠️** Agente `Disconnected` → escenario 06; no reiniciar el manager en vivo.

**📌** ✅ 18-nov.

### PREGUNTA S2 · ¿Cómo llega un evento de SSH hasta Wazuh?
**🗣️ ORAL:** "sshd del bastión escribe en `auth.log`; el agente Wazuh 001 lee ese archivo, lo envía cifrado por TCP 1514 al manager en SIEM001; el manager lo decodifica, aplica reglas —por ejemplo la 5710 de intento SSH con usuario inexistente— y escribe la alerta en `alerts.json`."

**⌨️ (🟢):** `sudo tail -n 3 /var/ossec/logs/alerts/alerts.json` → campos `timestamp`, `rule`, `agent`, `full_log` (no mostrar secretos).

### PREGUNTA S3 · ¿Wazuh correlaciona las fuentes visualmente?
**🗣️ ORAL:** "No. No instalamos Indexer ni Dashboard por los 4 GiB de la VM. El manager sí aplica reglas y correlación, pero la consulta es por archivo JSON. Tampoco integramos VPN, proxy, nginx ni Windows AD como fuentes, y no medimos los 2.000 eventos por segundo."

**📌** ❌ dashboard · ⏳ fuentes adicionales.

### PREGUNTA S4 · ¿96 alertas son 96 ataques?
"No. Una alerta es un registro de seguridad que cumplió una regla (por ejemplo, uso de sudo); no necesariamente un incidente."

### PREGUNTA S5 · ¿Qué problema encontraron?
"El primer conteo por fecha daba cero por el formato del timestamp; lo corregimos con `datetime.strptime` y el patrón `%Y-%m-%dT%H:%M:%S.%f%z`."

### PREGUNTA S6 · ¿Por qué el AD no envía eventos a Wazuh?
"No instalamos agente Wazuh en AD001 (sin Internet y canal de instalación limitado). Es un pendiente: requeriría agente Windows y regla 1514 desde 10.1.100.100 (ya cubierta por 10.1.0.0/16 en la regla 190)."
