# Prueba 6 · Administración de TI (grado 3) — 🟠 (prueba "de Esteban")

[⬅ Índice pruebas](00_INDICE_PRUEBAS.md) · [Guion Esteban](../ESTEBAN/09_GUION_DEMOSTRACION.md)

**Rúbrica:** "Un ingeniero en la red privada se conecta por SSH y RDP a los servidores. Un servidor interno no tiene acceso a internet. El monitoreo de infraestructura detecta un cambio de comportamiento en CPU, RAM o disco de algún servidor. Hay un SIEM o monitoreo de logs con ingesta de múltiples fuentes."

**Responsables:** Esteban (bastión, NOC, SIEM) + Escobar (red) + Nicolás (AD). **Cuatro partes en 90 s.** Orden de valor si falta tiempo: SSH/RDP → monitoreo → sin Internet → SIEM.

## Estado
| Parte | Estado |
|---|---|
| SSH por bastión | ✅ |
| RDP a AD001 por túnel | ✅ túnel TCP (Esteban) · ✅ sesión reportada por consolidado/Nicolás · ⚠️ requiere credencial `LANGUSTINO\ssdeti` del gestor |
| Servidor interno sin Internet | ⏳ sin evidencia registrada (prueba propuesta en AD001 o SQL001) |
| Cambio de CPU visible | ✅ métricas en Grafana; ⏳ pico en vivo no ensayado; alerta CPU DKR001 configurada, **nunca disparada** |
| SIEM multifuente | ✅ 4 fuentes Linux, 96 alertas en 30 min (18-nov) |

## Preparar antes (Mac Esteban)
```bash
# [MAC] pestaña 5
ssh -N -L 3000:127.0.0.1:3000 dkr1
# [MAC] pestaña 6
ssh -o ExitOnForwardFailure=yes -N -L 13389:10.1.100.100:3389 bastion
```
Grafana abierta en **LANgustino - Monitoreo NOC**, Job = **bastion**, rango *Last 5 minutes*, refresco 5–10 s.

## Pasos

| # | Dónde | Hacer | Ver |
|---|---|---|---|
| 1 | `[MAC]` 🟢 | `ssh -J bastion ssdeti@10.1.100.70` (o `ssh dkr1`) | Prompt SRVD1DEVERP001 / DKR001 |
| 2 | `[MAC]` Windows App | `localhost:13389`, usuario `LANGUSTINO\ssdeti` | Escritorio SRVD1DEVAD001 |
| 3 | `[AD001/POWERSHELL]` 🟢 | `Test-NetConnection www.google.com -Port 443` | `TcpTestSucceeded : False` (⏳ sin ensayo registrado) |
| 3' | Alternativa `[SQL001]` 🟢 | `curl -m 5 -I https://www.example.com` | Timeout (⏳) |
| 4 | `[NAVEGADOR/GRAFANA]` | http://localhost:3000 → LANgustino - Monitoreo NOC → Job bastion | CPU, RAM, disco, red |
| 5 | `[BASTION]` 🟡 temporal | `for i in 1 2; do timeout 60 nice -n 19 yes > /dev/null & done; wait` | En 15–30 s la CPU del bastión sube cerca de 100 % y baja al terminar |
| 6 | `[SIEM001]` 🟢 | `sudo /var/ossec/bin/agent_control -l` | 000 SIE001 Active/Local · 001 Bastión · 002 DKR001 · 003 DKR002 Active |
| 7 | `[SIEM001]` 🟢 | `sudo grep -o '"agent":{"id":"[0-9]*","name":"[^"]*"' /var/ossec/logs/alerts/alerts.json \| sort \| uniq -c` | Conteo por agente: varias fuentes en un solo lugar |

> El paso 5 cambia estado de forma controlada: `timeout 60` lo detiene solo; `nice -n 19` le da la prioridad más baja. Es la forma documentada por el equipo. Si algo queda vivo: `pkill -f "yes"` en el bastión.

## Qué decir
"Soy el ingeniero de TI, en la red privada por VPN. Entro siempre por el bastión, sin IP pública y único origen que las NSG aceptan para SSH y RDP. Salto al ERP por SSH y al controlador de dominio por un túnel RDP. El AD es un servidor interno sin salida a Internet: la conexión a Google falla. En Grafana, Prometheus recoge métricas cada 15 segundos; cargo la CPU del bastión un minuto y la gráfica lo muestra. Y Wazuh recibe eventos de cuatro servidores en un solo lugar, por ejemplo sudo, PAM e intentos SSH."

**Componentes:** Mac → VPN001 → BAS001:22 → ERP001:22 / AD001:3389 (túnel) · NOC001 (Prometheus 15 s) → node_exporter :9100 · Grafana :3000 vía túnel · Agentes Wazuh → SIE001:1514.

## Si preguntan
- *NOC vs SIEM:* "El NOC mide disponibilidad y recursos; el SIEM recoge y correlaciona eventos de seguridad. Wazuh corre sin indexador ni dashboard por recursos: las alertas quedan en `alerts.json`."
- *¿Por qué bastión?* "Un único punto de entrada administrativa, sin exponer SSH/RDP, con registro de quién entró."

## Si falla
| Síntoma | Acción |
|---|---|
| SSH espera | VPN o BAS001 apagada |
| Permission denied | Llave no autorizada (avisar a Escobar/Esteban) |
| RDP no abre | Terminal del túnel debe seguir abierta; AD001 encendida |
| Grafana no responde | Reabrir túnel 3000 |
| CPU no se mueve | Esperar 30 s (intervalo de scrape 15 s + rate 5m) |
