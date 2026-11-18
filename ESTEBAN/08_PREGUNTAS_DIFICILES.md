# 08 · Preguntas difíciles — Esteban

[⬅ Índice Esteban](00_INDICE_ESTEBAN.md)

## Originales del documento de Esteban (18-nov)

| Pregunta | Respuesta (≤20 s) | Archivo |
|---|---|---|
| ¿Diferencia NOC vs SIEM? | NOC mide rendimiento/disponibilidad; SIEM recolecta y analiza eventos de seguridad. | [02](02_NOC_PROMETHEUS.md), [06](06_SIEM_WAZUH.md) |
| ¿Qué prueba UP=1? | Que Prometheus obtuvo la métrica en ese instante; no garantiza uptime histórico. | [02](02_NOC_PROMETHEUS.md) |
| ¿Qué cambió en CPU Windows? | El dashboard usaba `windows_cpu_time_total`; Exporter 0.32.1 expone `windows_cpu_time_seconds_total`. | [03](03_GRAFANA.md) |
| ¿Por qué NTP central? | Trazabilidad temporal consistente entre servidores y eventos de auditoría. | [05](05_NTP.md) |
| ¿AD001 ya usa el NTP de DKR001? | No confirmado: respondió UDP 123, faltan `w32tm` y verificar el rol PDC. | [05](05_NTP.md) |
| ¿Nicolás puede acceder por RDP? | Se validaron cuenta SSH, TCP 3389 y túnel local; no se probó su autenticación Windows. | [01](01_BASTION.md) |
| ¿Wazuh correlaciona 4 fuentes visualmente? | Hay eventos de cuatro Linux, pero no está el Indexer/Dashboard. | [06](06_SIEM_WAZUH.md) |
| ¿NVR graba automáticamente? | No; RTSP y un MP4 de prueba. | [07](07_NVR_MEDIAMTX_FFMPEG.md) |
| ¿Por qué RDP/Grafana no están en Internet? | VPN y túneles SSH reducen la exposición de puertos administrativos. | [01](01_BASTION.md) |
| ¿Probaron el disparo de la alerta CPU? | No; regla configurada, sin prueba de estrés ni disparo real. | [03](03_GRAFANA.md) |

## Hipotéticas adicionales (no están en la rúbrica)

| Nivel | Pregunta | Respuesta (≤20 s) |
|---|---|---|
| Básica | ¿Qué es node_exporter? | Agente que publica métricas del SO Linux en TCP 9100 para que Prometheus las lea (modelo *pull*). |
| Básica | ¿Pull o push? | Prometheus hace *pull* cada 15 s; Wazuh es *push*: el agente envía al manager. |
| Intermedia | ¿Qué puertos abrieron para el NOC? | 9100 desde 10.1.100.41 hacia BAS001 (165), DKR002 (250), SIE001 (260); 9182 hacia AD001 (290). |
| Intermedia | ¿Dónde está la configuración de Prometheus? | En DKR001, montada en el contenedor `SRVD1DEVNOC001-prometheus` (ruta exacta no documentada en las guías; verificar con `sudo docker inspect SRVD1DEVNOC001-prometheus --format '{{json .Mounts}}'` 🟢). |
| Intermedia | ¿Qué pasa si Prometheus se reinicia? | `unless-stopped` lo levanta si el daemon reinicia; ya se vio `Exited (0)` una vez y se inició manualmente. Persistencia de datos depende del volumen (no verificada). |
| Avanzada | ¿Cuánta retención tiene Prometheus? | No documentada; por defecto Prometheus retiene 15 días. No afirmar valor configurado. |
| Avanzada | ¿Qué pasa si DKR001 se apaga? | Se pierde el NOC, el NTP central (clientes caen a PHC0, que conservaron como respaldo), y además LBR001, LMS, DNS, proxy filtro, PKI, Samba y backups. Wazuh sigue en SIE001. |
| Avanzada | ¿Cómo escalaría el SIEM? | Wazuh Indexer + Dashboard en VM más grande (≥8 GiB) o clúster; agentes en AD (Windows), VPN, nginx y Squid. |
| Avanzada | ¿Alertas por correo? | No configuradas (no hay evidencia). Grafana podría usar el MTA interno como contact point. |
| Crítica | ¿Por qué decir que hay SIEM si no hay dashboard? | La rúbrica pide "SIEM o monitoreo de logs con ingesta de múltiples fuentes": el manager recibe y evalúa eventos de 4 fuentes; el dashboard es visualización. |
| Crítica | ¿Por qué la alerta es de DKR001 y la carga se hace en el bastión? | La rúbrica pide que el monitoreo **detecte el cambio** (se ve en la gráfica); la alerta es una capacidad adicional, no probada. Cargar el bastión evita afectar DKR001, que aloja LBR001 y otros servicios. |
| Crítica | ¿Por qué no probaste RDP autenticado? | No tengo credenciales autorizadas del dominio y no corresponde adivinarlas; el túnel y TCP 3389 sí están probados. |
| Recorrido | Ingreso de un administrador por VPN y bastión hasta AD001 | Ver [PREGUNTAS_TRANSVERSALES/12_FLUJOS_ENTRE_SISTEMAS.md](../PREGUNTAS_TRANSVERSALES/12_FLUJOS_ENTRE_SISTEMAS.md) flujo F5. |
