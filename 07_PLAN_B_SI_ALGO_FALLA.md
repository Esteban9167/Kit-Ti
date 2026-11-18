# 07 · Plan B si algo falla

[⬅ README](README.md) · [Escenarios de fallo detallados](ESCENARIOS_DE_FALLO/01_SERVIDOR_APAGADO.md)

## Regla general (decirla con calma)

> "Este servicio no responde en este momento. Tengo la evidencia del ensayo del [fecha], y el diagnóstico es [X]. No voy a reiniciar nada en vivo para no afectar otras pruebas."

1. **No reiniciar servicios** ni cambiar NSG, sshd, GPO o DNS durante la sesión.
2. Diagnosticar **solo con comandos 🟢**.
3. Mostrar la **captura fechada** y decir de qué día es.
4. Explicar **qué componentes participan** (la rúbrica da 2,5 si se cumple un paso o falta la explicación).

## Árbol de decisión rápido

| Síntoma | Primera comprobación 🟢 | Archivo |
|---|---|---|
| Nada responde / timeout en SSH | ¿VPN conectada? ¿VM encendida en portal? | [01](ESCENARIOS_DE_FALLO/01_SERVIDOR_APAGADO.md), [02](ESCENARIOS_DE_FALLO/02_SSH_NO_CONECTA.md) |
| VPN rechaza | clave + 6 dígitos pegados, esperar siguiente código | [03](ESCENARIOS_DE_FALLO/03_VPN_NO_CONECTA.md) |
| Grafana "No data" | túnel abierto; target UP en Prometheus | [04](ESCENARIOS_DE_FALLO/04_GRAFANA_SIN_DATOS.md) |
| Target DOWN | ¿VM encendida? ¿exporter? ¿NSG 9100/9182? | [05](ESCENARIOS_DE_FALLO/05_PROMETHEUS_TARGET_DOWN.md) |
| Wazuh sin agentes | `agent_control -l` | [06](ESCENARIOS_DE_FALLO/06_WAZUH_SIN_AGENTES.md) |
| NTP no sincroniza | `chronyc sources -v` | [07](ESCENARIOS_DE_FALLO/07_NTP_NO_SINCRONIZA.md) |
| NVR sin video | `docker logs SRVD1DEVNVR001` | [08](ESCENARIOS_DE_FALLO/08_NVR_SIN_VIDEO.md) |
| AD no responde | `Test-NetConnection 10.1.100.100 -Port 389` / `nc -vz` | [09](ESCENARIOS_DE_FALLO/09_ACTIVE_DIRECTORY_NO_RESPONDE.md) |
| Contenedor `Exited` | `docker ps -a` | [10](ESCENARIOS_DE_FALLO/10_DOCKER_CONTENEDOR_DETENIDO.md) |
| Web 403/502/no carga | hosts, VPN, ERP001/DKR002 encendidas | [11](ESCENARIOS_DE_FALLO/11_APLICACION_WEB_NO_CARGA.md) |

## Dependencias críticas (si cae X, caen estas pruebas)

| Si cae… | Pruebas afectadas |
|---|---|
| VPN001 | 1, 2, 3, 4, 5, 6, 7, 8, 10 (todo menos 9) |
| BAS001 | 6 (SSH/RDP), logs de 4/5/8 |
| DKR001 | 4, 6 (NOC), 7, 8, 9, 10 (LBR001 publica todo lo web) |
| DKR002 | 1 (PBX/IVR), 5 (correo), 10 (CMS), MSA |
| ERP001 / SQL001 | 1, 2, 3, 7 (y LMS/CMS/MSA si SQL001) — la rúbrica lo advierte explícitamente |
| AD001 | 2 (autenticación VPN), RDP de 6 |
| SIE001 | parte SIEM de 6 |

## Capturas que deben estar a mano (carpeta local, sin Internet)

| ID | Contenido | Dueño | Estado según documentos |
|---|---|---|---|
| BAS-01 | `sshd -t`, `sshd -T`, AllowUsers | Esteban | ✅ |
| BAS-03 | túnel 13389 + `nc succeeded` | Esteban | ✅ |
| NOC-08/09 | Grafana AD001 + `up` 6 series | Esteban | ✅ |
| NOC-10..13 | Grafana DKR002, Bastión, SIEM001, DKR001 | Esteban | ✅ |
| NTP-01..03 | `chronyc` en DKR002, SIEM001, Bastión | Esteban | ✅ |
| SIEM-01/02 | `agent_control -l`, 29/5/18/44 | Esteban | ✅ |
| NVR-01/02 | logs RTSP, ffprobe, MP4 en Descargas | Esteban | ✅ |
| AD/GPO | vínculo GPO + valor 900 | Nicolás | ✅ |
| MSA | ticket #1 escalado y cerrado | Nicolás | ✅ |
| BKP | restauración 58 tablas | Nicolás | ✅ |
| IVR | S00001 en Odoo + llamada | Daniel | ✅ |
| Certificado | candado + Let's Encrypt | Daniel | ✅ |
| LMS | Moodle desde datos móviles | Daniel | ✅ |
| VPN vendedor | Connected + 10.8.0.x + Test-NetConnection True | Escobar | ✅ |
| Proxy | curl 403/200 desde DKR001 | Pulido | ✅ |
| DNS/DHCP | dig + DORA | Pulido | ✅ |

> ⚠️ Las capturas existen según las guías; **este kit no las contiene** (no fueron adjuntadas). Verificar que cada dueño las tenga en su equipo.

## Si cae Internet del salón

- Usar celular como hotspot (la VPN es UDP 1194; si la red del salón bloquea UDP, el hotspot lo resuelve).
- Prueba 9 se hace desde el celular con datos de todos modos.
- Si nada conecta: diagrama + capturas + explicación de flujo (componentes y puertos) para rescatar 2,5 donde sea posible.
