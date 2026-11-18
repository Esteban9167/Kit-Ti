# 01 · Mapa rápido de preguntas (buscador manual)

[⬅ README](README.md) · Usa **Cmd+F / Ctrl+F** con la palabra clave. Equipo = dónde ejecutar. 🟢 lectura · 🟡 cambia estado.

## A. Pruebas de recorrido (sorteo)
| Si me toca… | Archivo | Equipo | Lo clave |
|---|---|---|---|
| Prueba 1 IVR | [P01](PRUEBAS_RECORRIDO/P01_IVR.md) | PC + MicroSIP TCP | S00001, NIT 900123456, ext. 500 |
| Prueba 2 vendedor | [P02](PRUEBAS_RECORRIDO/P02_VENDEDOR_REMOTO.md) | PC Escobar → VEN001 | PartOfDomain, Test-NetConnection 389, 10.8.0.x |
| Prueba 3 SSO | [P03](PRUEBAS_RECORRIDO/P03_SSO_AUTENTICACION.md) | Navegador | No implementado → Keycloak/OIDC |
| Prueba 4 filtro | [P04](PRUEBAS_RECORRIDO/P04_FILTRO_NAVEGACION.md) | VEN001 + DKR001 | `access.log` de SRVD1DEVPRX001 |
| Prueba 5 correo | [P05](PRUEBAS_RECORRIDO/P05_CORREO.md) | VEN001 + DKR002 | Roundcube, 554 |
| Prueba 6 admin TI | [P06](PRUEBAS_RECORRIDO/P06_ADMINISTRACION_TI.md) | Mac Esteban | SSH, túnel RDP, Grafana, `agent_control` |
| Prueba 7 certificado | [P07](PRUEBAS_RECORRIDO/P07_CERTIFICADO_DIGITAL.md) | PC con VPN | Let's Encrypt, 15-feb-2027 |
| Prueba 8 software | [P08](PRUEBAS_RECORRIDO/P08_SOFTWARE_AUTORIZADO.md) | VEN001 + DKR001 | AnyDesk 403, smbclient |
| Prueba 9 LMS | [P09](PRUEBAS_RECORRIDO/P09_LMS.md) | Celular con datos | candado |
| Prueba 10 CMS | [P10](PRUEBAS_RECORRIDO/P10_CMS_INTRANET.md) | PC VPN + celular | noticia + 403 |

## B. Verificación de servidores / diagrama
| Si preguntan por… | Archivo | Equipo | Comando |
|---|---|---|---|
| vCPU/RAM/disco/IP de una VM | [08](08_VERIFICACION_EN_VIVO_3_SERVIDORES.md) | Portal / Cloud Shell | `az vm list -d …` 🟢 |
| Tamaño Azure (B2als_v2…) | [SANTIAGO/05](SANTIAGO/05_MATRIZ_APROVISIONAMIENTO.md) | Portal | Información general |
| Región / subred / CIDR | [SANTIAGO/02](SANTIAGO/02_VNET_SUBREDES_NSG.md) | Portal | Redes |
| Contenedores de un host | [03](03_INVENTARIO_SERVIDORES_IP_PUERTOS.md) | DKR001/DKR002 | `sudo docker ps` 🟢 |
| Por qué 3 regiones | [SANTIAGO/01](SANTIAGO/01_ARQUITECTURA_AZURE.md) | — | 6 vCPU/región |
| Guion del diagrama | [10](10_SUSTENTACION_DIAGRAMA.md) | — | — |

## C. Componentes de Esteban
| Si preguntan por… | Archivo | Equipo | Comando |
|---|---|---|---|
| Bastión endurecido | [ESTEBAN/01](ESTEBAN/01_BASTION.md) | BASTION | `sudo sshd -T \| grep …` 🟢 |
| Túnel RDP | [ESTEBAN/01](ESTEBAN/01_BASTION.md) | MAC | `ssh -N -L 13389:10.1.100.100:3389 bastion` + `nc -vz` |
| Targets de Prometheus | [ESTEBAN/02](ESTEBAN/02_NOC_PROMETHEUS.md) | DKR001 | `curl … query=up` 🟢 |
| CPU de AD001 | [ESTEBAN/04](ESTEBAN/04_WINDOWS_EXPORTER_AD001.md) | DKR001 / Grafana | `up{job="ad001"}` 🟢 |
| Dashboard / alerta CPU | [ESTEBAN/03](ESTEBAN/03_GRAFANA.md) | NAVEGADOR/GRAFANA | Alerting → rules |
| Métrica `windows_cpu_time_seconds_total` | [ESTEBAN/03](ESTEBAN/03_GRAFANA.md) | Grafana | PromQL |
| NTP de Bastión / DKR002 / SIEM | [ESTEBAN/05](ESTEBAN/05_NTP.md) | BASTION / DKR002 / SIEM001 | `chronyc tracking` 🟢 |
| NTP de AD001 / PDC | [ESTEBAN/05](ESTEBAN/05_NTP.md) | DKR002 | `chronyd -Q` (⏳ w32tm) |
| Agentes de Wazuh | [ESTEBAN/06](ESTEBAN/06_SIEM_WAZUH.md) | SIEM001 | `agent_control -l` 🟢 |
| Alertas por fuente | [ESTEBAN/06](ESTEBAN/06_SIEM_WAZUH.md) | SIEM001 | grep/uniq o script Python 🟢 |
| Evento SSH → Wazuh | [ESTEBAN/06](ESTEBAN/06_SIEM_WAZUH.md) | SIEM001 | `tail alerts.json` 🟢 |
| Grabación del NVR | [ESTEBAN/07](ESTEBAN/07_NVR_MEDIAMTX_FFMPEG.md) | DKR002 / MAC | `docker logs` / ffprobe / `open` |
| RTSP | [ESTEBAN/07](ESTEBAN/07_NVR_MEDIAMTX_FFMPEG.md) | DKR002 | logs MediaMTX |
| NOC vs SIEM | [ESTEBAN/08](ESTEBAN/08_PREGUNTAS_DIFICILES.md) | — | — |

## D. Componentes de Nicolás
| Si preguntan por… | Archivo | Equipo | Comando |
|---|---|---|---|
| Dominio, OUs, grupos, usuarios | [NICOLAS/01](NICOLAS/01_ACTIVE_DIRECTORY.md) | AD001/POWERSHELL | `Get-ADDomain` … 🟢 |
| GPO del dominio | [NICOLAS/02](NICOLAS/02_GPO.md) | AD001/POWERSHELL | `Get-GPInheritance`, `Get-GPRegistryValue` 🟢 |
| GPO configurada vs aplicada | [NICOLAS/02](NICOLAS/02_GPO.md) | Cliente | `gpresult /r` (⏳) |
| CA interna | [NICOLAS/03](NICOLAS/03_PKI_CERTIFICADOS.md) | DKR001 | `curl --cacert … /health` 🟢 |
| Samba / software autorizado | [NICOLAS/04](NICOLAS/04_SERVIDOR_ARCHIVOS.md) | DKR001 | `smbclient` 🟢 |
| Ticket Redmine | [NICOLAS/05](NICOLAS/05_MESA_AYUDA_REDMINE.md) | Navegador | http://10.1.100.102:8081 ticket #1 |
| Backups / restauración | [NICOLAS/06](NICOLAS/06_BACKUPS_RESTIC.md) | DKR001 | `crontab -l`, logs 🟢 |

## E. Componentes de Daniel
| Si preguntan por… | Archivo | Equipo | Comando |
|---|---|---|---|
| ERP / Odoo | [DANIEL/01](DANIEL/01_ERP_ODOO_INTEGRACION.md) | Navegador VPN | https://erp… |
| PostgreSQL | [PREGUNTAS_TRANSVERSALES/03](PREGUNTAS_TRANSVERSALES/03_BASES_DE_DATOS.md) | SQL001 | `psql -c '\l'` ◇ |
| PBX | [DANIEL/02](DANIEL/02_PBX_ASTERISK.md) | MicroSIP | ext. 101 |
| IVR | [DANIEL/03](DANIEL/03_IVR.md) | MicroSIP / ERP | 500 |
| Certificado / LBR001 / TLS | [DANIEL/04](DANIEL/04_CERTIFICADOS_HTTPS.md) | Navegador | candado |
| Moodle | [DANIEL/05](DANIEL/05_LMS_MOODLE.md) | Celular | https://lms… |
| Drupal | [DANIEL/06](DANIEL/06_CMS_DRUPAL.md) | Navegador VPN | https://cms… |

## F. Componentes de Santiago Escobar
| Si preguntan por… | Archivo | Equipo | Comando |
|---|---|---|---|
| NSG / regla verificada | [SANTIAGO/02](SANTIAGO/02_VNET_SUBREDES_NSG.md) | Azure | `az network nsg rule list …` 🟢 |
| Peering / NAT / salida Internet | [SANTIAGO/03](SANTIAGO/03_ENRUTAMIENTO_CONECTIVIDAD.md) | — | — |
| IP públicas | [SANTIAGO/04](SANTIAGO/04_FIREWALL_SEGURIDAD.md) | Portal | — |
| VPN + TOTP | [SANTIAGO/08](SANTIAGO/08_VPN_OPENVPN_TOTP.md) | Cloud Shell | `run-command … auth.log` 🟢 |
| VM del vendedor | [SANTIAGO/09](SANTIAGO/09_VM_VENDEDOR_VEN001.md) | VEN001 | `Get-CimInstance …` 🟢 |

## G. Componentes de Santiago Pulido
| Si preguntan por… | Archivo | Equipo | Comando |
|---|---|---|---|
| Squid / dominios bloqueados | [PULIDO/01](OTROS_INTEGRANTES/PULIDO/01_PROXY_FILTRO_SQUID.md) | DKR001 | `curl -x … facebook` 🟢 |
| DNS interno / externo | [PULIDO/02](OTROS_INTEGRANTES/PULIDO/02_DNS001_DNS003.md) | DKR001 | `dig @10.1.100.41 …` 🟢 |
| DHCP | [PULIDO/03](OTROS_INTEGRANTES/PULIDO/03_DHCP_KEA.md) | DKR001 | DORA 🟡 |
| Correo | [PULIDO/04](OTROS_INTEGRANTES/PULIDO/04_CORREO_POSTFIX_ROUNDCUBE.md) | DKR002 | `docker logs SRVD1DEVMTA001` 🟢 |

## H. Preguntas transversales
| Si preguntan… | Archivo |
|---|---|
| Medidas de seguridad / públicos vs privados | [01_SEGURIDAD](PREGUNTAS_TRANSVERSALES/01_SEGURIDAD.md) |
| Servicios que dependen de DNS / comunicación entre servidores | [02_RED](PREGUNTAS_TRANSVERSALES/02_RED_Y_CONECTIVIDAD.md) |
| ¿Qué pasa si falla PostgreSQL? | [03_BD](PREGUNTAS_TRANSVERSALES/03_BASES_DE_DATOS.md) |
| ¿Qué comparten DKR002? / ¿si se apaga DKR001? | [04_DOCKER](PREGUNTAS_TRANSVERSALES/04_CONTENEDORES_DOCKER.md) |
| Caída del servidor vs exportador | [05_MONITOREO](PREGUNTAS_TRANSVERSALES/05_MONITOREO_Y_ALERTAS.md) |
| NTP y Kerberos / SSO vs LDAP | [06_IDENTIDAD](PREGUNTAS_TRANSVERSALES/06_IDENTIDAD_Y_AUTENTICACION.md) |
| ¿Dónde termina TLS? | [07_CERTIFICADOS](PREGUNTAS_TRANSVERSALES/07_CERTIFICADOS_Y_CIFRADO.md) |
| ¿Backup restaurable? RPO/RTO | [08_BACKUP](PREGUNTAS_TRANSVERSALES/08_BACKUP_Y_RECUPERACION.md) |
| Alta disponibilidad | [09_HA](PREGUNTAS_TRANSVERSALES/09_ALTA_DISPONIBILIDAD.md) |
| Costos / escalabilidad | [10_COSTOS](PREGUNTAS_TRANSVERSALES/10_COSTOS_Y_ESCALABILIDAD.md) |
| ¿Qué falta? / limitaciones | [11_RIESGOS](PREGUNTAS_TRANSVERSALES/11_RIESGOS_Y_LIMITACIONES.md), [PENDIENTES](REFERENCIAS/PENDIENTES_REALES.md) |
| Flujo de extremo a extremo | [12_FLUJOS](PREGUNTAS_TRANSVERSALES/12_FLUJOS_ENTRE_SISTEMAS.md) |
| ¿Problemas reales y soluciones? | [CAMBIOS_RECIENTES](REFERENCIAS/CAMBIOS_RECIENTES.md) |
| IaC / automatización | [04_ARQUITECTURA §4](04_ARQUITECTURA_GENERAL.md) |
| ¿Qué parte hizo cada compañero? | [README](README.md) (índices por integrante) |
| ¿Qué puedo demostrar sin modificar nada? | Todo comando 🟢 en [06_COMANDOS_MAESTROS](06_COMANDOS_MAESTROS.md) |

## I. Algo falló
| Síntoma | Archivo |
|---|---|
| VM apagada | [ESCENARIOS_DE_FALLO/01](ESCENARIOS_DE_FALLO/01_SERVIDOR_APAGADO.md) |
| SSH | [02](ESCENARIOS_DE_FALLO/02_SSH_NO_CONECTA.md) |
| VPN | [03](ESCENARIOS_DE_FALLO/03_VPN_NO_CONECTA.md) |
| Grafana sin datos | [04](ESCENARIOS_DE_FALLO/04_GRAFANA_SIN_DATOS.md) |
| Target DOWN | [05](ESCENARIOS_DE_FALLO/05_PROMETHEUS_TARGET_DOWN.md) |
| Wazuh | [06](ESCENARIOS_DE_FALLO/06_WAZUH_SIN_AGENTES.md) |
| NTP | [07](ESCENARIOS_DE_FALLO/07_NTP_NO_SINCRONIZA.md) |
| NVR | [08](ESCENARIOS_DE_FALLO/08_NVR_SIN_VIDEO.md) |
| AD | [09](ESCENARIOS_DE_FALLO/09_ACTIVE_DIRECTORY_NO_RESPONDE.md) |
| Contenedor detenido | [10](ESCENARIOS_DE_FALLO/10_DOCKER_CONTENEDOR_DETENIDO.md) |
| Web 403/502 | [11](ESCENARIOS_DE_FALLO/11_APLICACION_WEB_NO_CARGA.md) |
