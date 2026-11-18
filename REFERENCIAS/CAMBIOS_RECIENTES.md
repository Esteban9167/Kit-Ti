# Cambios recientes y contradicciones resueltas

[⬅ README](../README.md)

| # | Tema | Documento antiguo dice | Evidencia más reciente | Qué usar |
|---|---|---|---|---|
| 1 | Windows Exporter AD001 | No instalado, sin métricas (Nicolás, guía técnica) | Instalado 0.32.1, `up{job="ad001"}=1`, Grafana con CPU 2,47 % (Esteban 18-nov) | **Instalado y funcionando** |
| 2 | Proxy de la prueba 4 | PXY001 10.1.100.40:3128 (Escobar 17-nov) | PRX001 10.1.100.41:3128 (consolidado 17-nov, Pulido 18-nov, probado con curl) | **10.1.100.41:3128**; regla 129 por confirmar |
| 3 | NVR puerto | 8085 TCP (guía técnica, plan) | RTSP 127.0.0.1:8554 (Esteban) | **8554 loopback** |
| 4 | Estado de servicios DKR001/DKR002 "PLAN" | DNS, DHCP, NTP, LBR, PBX, IVR, LMS, CMS, NVR "por verificar" (guía técnica) | Probados en documentos de Daniel, Pulido, Esteban | Ver estados por archivo |
| 5 | GPO 900 s | "Propuesto, no confirmado" (guía técnica) | Valor leído en AD/SYSVOL (Nicolás 18-nov) | **Configurada ✅, aplicada ⏳** |
| 6 | SIEM | "Reportado, configuración pendiente" (guía técnica) | Manager 4.14.10, 4 agentes, 96 alertas (Esteban) | ✅ |
| 7 | Región AD/SIEM/DKR002 | "Región 3" (plan) | Chile Central (guion del diagrama) | **Chile Central** |
| 8 | VM vendedor | "Ubicación e IP no verificadas" | Brazil South 192.168.50.4 / 20.226.80.153 | ✅ |
| 9 | Prueba 6 "sin Internet" | SQL001 con curl (Escobar) | AD001 con Test-NetConnection (consolidado) | Cualquiera; **sin evidencia registrada** |
| 10 | Prueba 6 SIEM | "Abrir panel de Wazuh" (Escobar) | No hay dashboard; `agent_control` + `alerts.json` (consolidado/Esteban) | **CLI** |
| 11 | Red interna permitida en CMS | 10.0.0.0/16 (texto del diagrama) | 10.1.0.0/16 (Daniel) | Decir **10.1.0.0/16** |
| 12 | RDP al AD | "Sí" (consolidado, Nicolás) | Esteban: túnel sí, sesión propia no | Sesión con `LANGUSTINO\ssdeti` por quien tenga la credencial |

## Problemas reales encontrados y cómo se resolvieron (respuesta a "¿qué problemas tuvieron?")

| Problema | Solución | Dueño |
|---|---|---|
| Panel CPU de Windows vacío (`windows_cpu_time_total` inexistente) | Consulta con `windows_cpu_time_seconds_total` | Esteban |
| Conteo de alertas Wazuh por fecha daba 0 | Parseo con `datetime.strptime('%Y-%m-%dT%H:%M:%S.%f%z')` | Esteban |
| Prometheus `Exited (0)` | Inicio manual; verificar antes de demo | Esteban |
| SSH intermitente (Broken pipe) | Reabrir sesiones; no tocar sshd | Esteban |
| MicroSIP falla por UDP | Transporte TCP | Daniel |
| NIT incorrecto | IVR responde "no encontramos" sin caerse | Daniel |
| Azure no propaga DHCP | Kea en red Docker aislada | Pulido |
| AD sin Internet para instalar exporter | MSI transferido de forma autorizada y borrado | Nicolás |
| Cuota 6 vCPU/región | 3 regiones + contenedores | Escobar |
| LDAPS falla en TLS / FQDN no resuelve | Pendiente (CSR generada) | Nicolás |
| SMB DKR002→DKR001 timeout | Pendiente revisión NSG | Escobar + Nicolás |
