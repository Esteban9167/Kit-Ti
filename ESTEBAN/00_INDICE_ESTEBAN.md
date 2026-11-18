# Esteban Sequeda — Índice

[⬅ README](../README.md)

**Componentes:** Bastión (REQ-ADM-03) · NOC Prometheus/Grafana (REQ-ADM-05) · Windows Exporter AD001 (integración con Nicolás) · NTP (REQ-ADM-04) · SIEM Wazuh · NVR. **Escena 9: administrador de TI → Prueba 6.**

| Archivo | Servidor | Estado |
|---|---|---|
| [01_BASTION.md](01_BASTION.md) | BAS001 10.1.120.10 | ✅ (RDP autenticado propio: ⏳) |
| [02_NOC_PROMETHEUS.md](02_NOC_PROMETHEUS.md) | DKR001 | ✅ 6 targets UP |
| [03_GRAFANA.md](03_GRAFANA.md) | DKR001 (túnel) | ✅ (alerta CPU sin disparo) |
| [04_WINDOWS_EXPORTER_AD001.md](04_WINDOWS_EXPORTER_AD001.md) | AD001 ↔ DKR001 | ✅ |
| [05_NTP.md](05_NTP.md) | DKR001 → BAS001/DKR002/SIE001 | ✅ (AD001 fuente/PDC ⏳) |
| [06_SIEM_WAZUH.md](06_SIEM_WAZUH.md) | SIE001 10.1.100.101 | ✅ 4 fuentes Linux |
| [07_NVR_MEDIAMTX_FFMPEG.md](07_NVR_MEDIAMTX_FFMPEG.md) | DKR002 | ✅ (sin grabación continua) |
| [08_PREGUNTAS_DIFICILES.md](08_PREGUNTAS_DIFICILES.md) | — | — |
| [09_GUION_DEMOSTRACION.md](09_GUION_DEMOSTRACION.md) | Mac | — |

## Límites que Esteban declara SIEMPRE
1. Alerta "LANgustino - CPU Alta DKR001" (>80 % por 1 min) **configurada, nunca disparada**.
2. Fuente horaria de Windows / rol PDC Emulator de AD001 **no verificados**.
3. Wazuh **sin indexer ni dashboard**; fuentes VPN, proxy, nginx y Windows AD **no integradas**; 2.000 eventos/s **no medidos**.
4. NVR: **sin grabación continua automática**; el MP4 es captura de prueba.
5. Sesión RDP autenticada de Esteban en AD001 **no realizada** (sin credenciales autorizadas).
6. "UP" vale para el instante de la consulta; 96 alertas ≠ 96 ataques.
