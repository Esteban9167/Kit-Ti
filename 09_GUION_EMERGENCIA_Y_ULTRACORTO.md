# 09 · Guion de emergencia y versión ultracorta

[⬅ README](README.md) · [Mapa rápido](01_MAPA_RAPIDO_PREGUNTAS.md)

## Versión ultracorta

**Encuentra el procedimiento en < 15 s:** palabra clave → archivo.

| Palabra | Archivo | Máquina |
|---|---|---|
| IVR, NIT, pedido, MicroSIP | [P01](PRUEBAS_RECORRIDO/P01_IVR.md) | PC Daniel + MicroSIP |
| vendedor, VPN, TOTP, 10.8.0.x | [P02](PRUEBAS_RECORRIDO/P02_VENDEDOR_REMOTO.md) | PC Escobar → VEN001 |
| SSO, Keycloak, Redmine login | [P03](PRUEBAS_RECORRIDO/P03_SSO_AUTENTICACION.md) | Navegador |
| Facebook, proxy, Squid, filtro | [P04](PRUEBAS_RECORRIDO/P04_FILTRO_NAVEGACION.md) | VEN001 + DKR001 |
| correo, Roundcube, 554 | [P05](PRUEBAS_RECORRIDO/P05_CORREO.md) | VEN001 + DKR002 |
| SSH, RDP, CPU, Grafana, Wazuh | [P06](PRUEBAS_RECORRIDO/P06_ADMINISTRACION_TI.md) | Mac Esteban |
| HTTPS ERP, candado | [P07](PRUEBAS_RECORRIDO/P07_CERTIFICADO_DIGITAL.md) | Cualquier PC con VPN |
| AnyDesk, software, Samba | [P08](PRUEBAS_RECORRIDO/P08_SOFTWARE_AUTORIZADO.md) | VEN001 + DKR001 |
| Moodle, LMS | [P09](PRUEBAS_RECORRIDO/P09_LMS.md) | Celular con datos |
| Drupal, intranet, noticia | [P10](PRUEBAS_RECORRIDO/P10_CMS_INTRANET.md) | PC con VPN + celular |
| NTP, chrony, hora | [ESTEBAN/05_NTP.md](ESTEBAN/05_NTP.md) | `ssh dkr2` |
| NVR, cámara, RTSP | [ESTEBAN/07_NVR_MEDIAMTX_FFMPEG.md](ESTEBAN/07_NVR_MEDIAMTX_FFMPEG.md) | `ssh dkr2` |
| AD, GPO, OU | [NICOLAS/01_ACTIVE_DIRECTORY.md](NICOLAS/01_ACTIVE_DIRECTORY.md) | RDP AD001 |
| backup, restic, RPO | [NICOLAS/06_BACKUPS_RESTIC.md](NICOLAS/06_BACKUPS_RESTIC.md) | `ssh dkr1` |
| NSG, regla, peering | [SANTIAGO/02_VNET_SUBREDES_NSG.md](SANTIAGO/02_VNET_SUBREDES_NSG.md) | Portal |
| DNS, dig | [OTROS_INTEGRANTES/PULIDO/02_DNS001_DNS003.md](OTROS_INTEGRANTES/PULIDO/02_DNS001_DNS003.md) | `ssh dkr1` |
| tamaño VM, IP | [08](08_VERIFICACION_EN_VIVO_3_SERVIDORES.md) | Portal |

**Las 6 frases salvavidas:**
1. "Entramos por VPN con usuario de AD y TOTP; administración solo por el bastión."
2. "Solo hay dos IP públicas de servicio: VPN (UDP 1194) y balanceador (80/443)."
3. "Regla 4000 niega todo entre redes; abrimos IP→IP:puerto en el NSG del destino."
4. "NOC mide rendimiento; SIEM recoge eventos de seguridad."
5. "Esto está configurado pero no probado de punta a punta: lo pendiente es X."
6. "No reinicio nada en vivo; muestro la evidencia del ensayo con su fecha."

---

## Guion principal de demostración (minimiza cambios de máquina)

### Antes de empezar (T-15 min)

| Qué | Dónde |
|---|---|
| Encender VM (10) | `[AZURE PORTAL]` |
| VPN conectada | Mac Esteban / PC Daniel / PC Escobar |
| Pestañas de Terminal: `bastion`, `dkr1`, `dkr2`, `siem` | `[MAC]` |
| Túnel Grafana (pestaña 5) y túnel RDP 13389 (pestaña 6) | `[MAC]` |
| Navegador 1: Grafana (2 dashboards) | Chrome/Safari en Mac |
| Navegador 2: Odoo S00001 abierto, Drupal login | PC Daniel |
| RDP a VEN001 abierto, OpenVPN desconectado dentro de VEN001 | PC Escobar |
| Celular con datos (Wi-Fi off) con lms/cms abiertos | Daniel |
| Carpeta de capturas | Todos |
| `~/Downloads/camara1_prueba.mp4` | Mac Esteban |

### Orden sugerido por estación (si la profesora permite usar el equipo preparado)

| Estación | Pruebas / verificaciones | Comandos ya abiertos |
|---|---|---|
| **PC Daniel** | 1 IVR → 7 Certificado → 10 CMS → 3 SSO (explicación) | MicroSIP, Odoo, Drupal, Redmine |
| **Celular** | 9 LMS → 403 de 7 y 10 | lms/erp/cms en pestañas |
| **PC Escobar** | 2 Vendedor → 4 Filtro → 8 Software → 5 Correo (todas en VEN001) | RDP VEN001, terminal DKR001/DKR002 |
| **Mac Esteban** | 6 Administración TI, verificación de servidores, NTP/NVR si preguntan | 6 pestañas |

### Qué decir al cerrar cada demo
"Componentes que participaron: [origen] → [intermedio] → [destino], por [protocolo/puerto]. Resultado: [lo que se vio]."

### Si cae Internet, VPN o SSH
→ [07_PLAN_B_SI_ALGO_FALLA.md](07_PLAN_B_SI_ALGO_FALLA.md) (capturas por ID).
