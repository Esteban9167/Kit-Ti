# 05 · Matriz de rúbrica y evidencias

[⬅ README](README.md) · [Pruebas de recorrido](PRUEBAS_RECORRIDO/00_INDICE_PRUEBAS.md) · [Pendientes reales](REFERENCIAS/PENDIENTES_REALES.md)

Fuente: `Rubrica_v2.0_EntregaFinal.pdf` (texto literal del Anexo A) + documentos de pruebas del equipo (17 y 18 nov). **No se inventan porcentajes**: los puntos son los de la rúbrica.

## 1. Criterios (25 % cada uno)

| # | Criterio | Se evalúa sobre | Para nivel 5 | Dónde preparar |
|---|---|---|---|---|
| 1 | Diagrama de despliegue | `.drawio` | Iconografía oficial, CPU/RAM/disco en todos los nodos, VNet/subredes con CIDR, 3 zonas, regiones, NSG con puertos/protocolos, consistente con topología lógica | [10_SUSTENTACION_DIAGRAMA.md](10_SUSTENTACION_DIAGRAMA.md) |
| 2 | Coherencia diagrama ↔ despliegue | 3 servidores elegidos por la profesora | 100 % coincide, ningún recurso desplegado fuera del diagrama, cualquier integrante verifica solo | [08_VERIFICACION_EN_VIVO_3_SERVIDORES.md](08_VERIFICACION_EN_VIVO_3_SERVIDORES.md) |
| 3 | Pruebas de recorrido | 10 pruebas del Anexo A | ≥27 puntos **y** 5 en pruebas 1, 2 y 3 | [PRUEBAS_RECORRIDO/00_INDICE_PRUEBAS.md](PRUEBAS_RECORRIDO/00_INDICE_PRUEBAS.md) |
| 4 | Decisiones de implementación y seguridad | Preguntas | IaC + contenedores (+CI/CD), reproducibilidad, "¿qué pasaría si…?", varios integrantes responden con solidez | [04_ARQUITECTURA_GENERAL.md](04_ARQUITECTURA_GENERAL.md), [PREGUNTAS_TRANSVERSALES/](PREGUNTAS_TRANSVERSALES/01_SEGURIDAD.md) |

**Implicación honesta:** la prueba 3 (SSO) no está implementada → el criterio 3 queda **como máximo en nivel 4** aunque todo lo demás salga en 5 (regla: nivel 5 exige 5 en IVR, vendedor y autenticación).

Calificación por prueba: 5 (todos los pasos en vivo + explica componentes) · 2,5 (al menos un paso) · 0 (ninguno o no alcanzó). Puntos = grado × calificación ÷ 5.

## 2. Matriz de las 10 pruebas

| # | Requisito textual (Anexo A) | Pts | Responsable principal | Dependencias de otros | Estado verificado | Evidencia | Cómo demostrar | Riesgo | Alternativa |
|---|---|---|---|---|---|---|---|---|---|
| 1 | "Se demuestra algún caso de uso del IVR. Se consulta un pedido indicando el NIT, y ese pedido está guardado en el ERP." | 5 | Daniel | VPN001 (Escobar), NSG 220-240 (Escobar), SQL001, ERP001 | ✅ completa 17-nov (NIT correcto e incorrecto) | Pedido S00001, NIT 900123456, `ivr_ro` | [P01](PRUEBAS_RECORRIDO/P01_IVR.md) | Bajo-medio (MicroSIP TCP, VPN) | Mostrar S00001 en Odoo + explicar flujo |
| 2 | "Una máquina virtual de vendedor remoto, unida al dominio de Directorio Activo y desconectada de la red corporativa, se conecta a la VPN cliente. Se piden credenciales de Directorio Activo y un segundo factor. Tras autenticarse, accede al ERP desde una IP privada asignada por el servidor de VPN." | 4 | Santiago Escobar | AD001 (Nicolás), LBR001/ERP001/SQL001 (Daniel) | 🟠 dominio, VPN AD+TOTP, IP 10.8.0.x ✅; ERP desde VEN001 ⏳ | PartOfDomain True; vendedor1 autenticado | [P02](PRUEBAS_RECORRIDO/P02_VENDEDOR_REMOTO.md) | Medio: TOTP solo en celular de Escobar; RDP a VEN001 solo desde IP de Escobar | Mostrar pasos 1-5 (2,5 asegurado) |
| 3 | "Hay Single Sign-On configurado para la mesa de ayuda y el ERP: quien ya se autenticó en el ERP no vuelve a poner sus credenciales de Directorio Activo." | 4 | Nicolás + Daniel | AD001, ERP001, MSA001 | ❌ no implementado | Redmine pide credenciales de nuevo | [P03](PRUEBAS_RECORRIDO/P03_SSO_AUTENTICACION.md) | Alto (0 pts probable) | Explicar diseño Keycloak + OIDC (criterio 4) |
| 4 | "Desde la máquina virtual del vendedor conectado a la red privada, el intento de abrir una red social da un error que lo impide. El vendedor puede navegar a una página externa autorizada. El administrador del filtro revisa en los logs los intentos del vendedor." | 3 | Pulido + Escobar | VPN001, regla NSG 129 (3128 desde VPN) ⏳, proxy en VEN001 | 🟠 Facebook 403 / Google 200 desde DKR001 ✅; desde VEN001 ⏳ | curl con `-x http://10.1.100.41:3128` | [P04](PRUEBAS_RECORRIDO/P04_FILTRO_NAVEGACION.md) | Medio | Plan B curl desde DKR001 + log Squid |
| 5 | "Hay al menos dos buzones con el dominio de LANgustino. El vendedor envía un correo a otro empleado con éxito. Al intentar enviarse información de la empresa a su correo personal, la configuración del servidor se lo impide." | 3 | Pulido | Regla NSG 300 hacia webmail ⏳, VPN, VEN001 | 🟠 3 buzones (operario1, vendedor1, soporte1); envío interno reportado; 554 observado antes; URL webmail ⏳ | Reporte Pulido | [P05](PRUEBAS_RECORRIDO/P05_CORREO.md) | Alto | Logs de Postfix en DKR002 |
| 6 | "Un ingeniero en la red privada se conecta por SSH y RDP a los servidores. Un servidor interno no tiene acceso a internet. El monitoreo de infraestructura detecta un cambio de comportamiento en CPU, RAM o disco de algún servidor. Hay un SIEM o monitoreo de logs con ingesta de múltiples fuentes." | 3 | Esteban (+Escobar, Nicolás) | BAS001, AD001 (credencial RDP), DKR001, SIE001 | 🟠 SSH ✅, túnel RDP ✅, Grafana 5 servidores ✅, Wazuh 4 fuentes ✅; pico CPU en vivo y "sin Internet" sin ensayo registrado | Guía Esteban 18-nov | [P06](PRUEBAS_RECORRIDO/P06_ADMINISTRACION_TI.md) | Medio (4 partes en 90 s) | Capturas NOC/SIEM |
| 7 | "Se accede al ERP por HTTPS." | 2 | Daniel | VPN, LBR001, ERP001, SQL001 | ✅ | Let's Encrypt `*.langustino-lab.duckdns.org`, válido hasta 15-feb-2027 | [P07](PRUEBAS_RECORRIDO/P07_CERTIFICADO_DIGITAL.md) | Bajo | Captura del certificado |
| 8 | "Se demuestra que se deniega la descarga de software no autorizado a los empleados de LANgustino." | 2 | Pulido + Nicolás | PRX001, FIL001, VEN001 | 🟠 AnyDesk 403 desde DKR001 ✅; FIL001 solo lectura ✅; desde VEN001 ⏳ | curl 403; smbclient | [P08](PRUEBAS_RECORRIDO/P08_SOFTWARE_AUTORIZADO.md) | Medio | curl desde DKR001 |
| 9 | "El LMS es accesible desde internet y su URL tiene certificado SSL." | 2 | Daniel | DKR001 encendido | ✅ | Moodle con candado desde datos móviles | [P09](PRUEBAS_RECORRIDO/P09_LMS.md) | Bajo | — |
| 10 | "La intranet solo es accesible desde la red interna. El administrador de contenido publica una noticia interna. La URL tiene certificado SSL." | 2 | Daniel | VPN, LBR001, DKR002, SQL001 | 🟠 configurado; ejecución de punta a punta ⏳ | Regla allow/deny en LBR001 | [P10](PRUEBAS_RECORRIDO/P10_CMS_INTRANET.md) | Medio | Mostrar 403 + candado |
| | **Total** | **30** | | | | | | | |

## 3. Pruebas que requieren cooperación (¡no intentar solo!)

| Prueba | Quién debe tener algo listo | Qué |
|---|---|---|
| 1 | Quien la haga + Daniel | MicroSIP registrado (ext. 101 = un solo dispositivo; el que presenta se registra de último), sesión Odoo |
| 2 | **Santiago Escobar** | PC con IP permitida en `nsg-langustino-vnd`; **celular con TOTP de vendedor1** |
| 4, 5, 8 | **Escobar** (VEN001, reglas NSG 129 y 300) + **Pulido** (log Squid, URL webmail) | Proxy configurado en VEN001 |
| 6 | **Esteban** (alias, túneles, Grafana, Wazuh) + quien tenga credencial `LANGUSTINO\ssdeti` | RDP autenticado |
| 3 | Nicolás + Daniel | Solo explicación |
| 10 | Daniel | Usuario admin de Drupal; celular con datos |

Recomendación del consolidado: el integrante sorteado puede **usar el equipo ya preparado** (confirmarlo con la profesora al inicio).

## 4. Requisitos REQ de Esteban (referencia interna del equipo)

| REQ | Componente | Estado |
|---|---|---|
| REQ-ADM-03 | Bastión | ✅ (RDP autenticado de Nicolás vía bastión: no probado por Esteban) |
| REQ-ADM-04 | NTP | ✅ 3 clientes Linux; AD001 fuente/PDC ⏳ |
| REQ-ADM-05 | NOC | ✅ 6 targets UP; disparo de alerta ⏳ |
| REQ-SEC-01 | AD/GPO (Nicolás) | 🟠 GPO vinculada; aplicación en cliente ⏳ |
| REQ-COM-03 | Correo (Pulido) | 🟠 |
| REQ-COM-04 | Mesa de ayuda (Nicolás) | ✅ ticket; SSO ⏳ |
