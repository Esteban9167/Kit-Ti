# 06 · Wazuh sin agentes

[⬅ Plan B](../07_PLAN_B_SI_ALGO_FALLA.md) · [SIEM](../ESTEBAN/06_SIEM_WAZUH.md)

`[SIEM001]` 🟢:
```bash
sudo /var/ossec/bin/agent_control -l
sudo systemctl status wazuh-manager --no-pager      # ◇
sudo tail -n 20 /var/ossec/logs/ossec.log           # ◇
```
| Resultado | Causa | Acción |
|---|---|---|
| Agente `Disconnected` | VM del agente apagada o agente detenido | Ver VM; en el host del agente (◇ 🟢) `sudo systemctl status wazuh-agent --no-pager` |
| Todos desconectados | Manager caído / regla 190 | Mostrar conteo histórico de `alerts.json` + captura SIEM-01 |
| `alerts.json` sin eventos recientes | Poca actividad | Generar un evento legítimo: un `sudo` cualquiera en el bastión (🟢 lectura, p. ej. `sudo -l`) produce alerta PAM/sudo |
Reiniciar `wazuh-manager` = 🟡, evitar en vivo.
