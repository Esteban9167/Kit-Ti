# Prueba 2 · Acceso del vendedor remoto (grado 4) — 🟠

[⬅ Índice pruebas](00_INDICE_PRUEBAS.md) · [Santiago VPN](../SANTIAGO/08_VPN_OPENVPN_TOTP.md) · [VEN001](../SANTIAGO/09_VM_VENDEDOR_VEN001.md)

**Rúbrica:** "Una máquina virtual de vendedor remoto, unida al dominio de Directorio Activo y desconectada de la red corporativa, se conecta a la VPN cliente. Se piden credenciales de Directorio Activo y un segundo factor. Tras autenticarse, accede al ERP desde una IP privada asignada por el servidor de VPN."

**Responsable:** Santiago Escobar · **Dependencias:** AD001 (Nicolás), LBR001/ERP001/SQL001 (Daniel).

**Estado:** dominio ✅, VPN con AD+TOTP ✅, IP 10.8.0.x ✅ · **abrir ERP desde VEN001: ⏳ no ensayado** (requiere línea en `hosts` de VEN001).

## Requisitos para que otro integrante la haga
- Su IP pública agregada a `nsg-langustino-vnd` (lo hace Escobar, 🔴 antes, no en vivo).
- La cuenta **vendedor1** en su app autenticadora (llave por privado de Escobar). Si no: **usar el PC y celular de Escobar** (confirmar con la profesora).

## Pasos

| # | Dónde | Hacer | Ver |
|---|---|---|---|
| 1 | `[WINDOWS PC]` (Escobar) | `mstsc /v:20.226.80.153` usuario `.\ssdeti` | Escritorio SRVD1DEVVEN001 |
| 2 | `[VEN001/POWERSHELL]` 🟢 | `(Get-CimInstance Win32_ComputerSystem) \| Select Name, Domain, PartOfDomain` | `langustino.local`, `True` |
| 3 | `[VEN001/POWERSHELL]` 🟢 (VPN desconectada) | `ipconfig` y `Test-NetConnection 10.1.100.100 -Port 389` | 192.168.50.4 · `TcpTestSucceeded : False` |
| 4 | `[VEN001]` OpenVPN Connect | Usuario `vendedor1`; contraseña = clave AD + 6 dígitos pegados | Pide ambas; **Connected** |
| 5 | `[VEN001/POWERSHELL]` 🟢 | `ipconfig` y de nuevo `Test-NetConnection 10.1.100.100 -Port 389` | IP **10.8.0.x** · `True` |
| 6 | `[VEN001]` Edge | https://erp.langustino-lab.duckdns.org → login Odoo | Odoo con candado (⏳ ensayar) |

## Qué decir
"Esta es la máquina de un vendedor remoto en Brasil, en una red sin conexión con LANgustino, unida al dominio `langustino.local`. Sin VPN no ve el controlador de dominio. Con OpenVPN el servidor pide usuario y clave de AD y un código de seis dígitos. El servidor de VPN valida el código y consulta la clave al AD por sssd, porque también está unido al dominio. Me asigna una IP privada 10.8.0.x y ahora alcanzo los servidores. Abro el ERP por HTTPS: entra por el balanceador y llega a Odoo."

**Componentes:** VEN001 → pip-vpn UDP 1194 → VPN001 (PAM: `pam_google_authenticator` + `pam_sss` → AD001) → NAT 10.1.110.10 → LBR001 :443 (DKR001) → ERP001 :8069 → SQL001 :5432.

## Si preguntan
- *¿Por qué no RADIUS?* "RAD001 con FreeRADIUS y TOTP existe y se probó en Fase 2. Para la VPN dejamos PAM con sssd porque ya estaba integrado con OpenVPN y el dominio, con menos piezas que fallar. Para RADIUS habría que abrir UDP 1812 de la VPN a DKR001."
- *¿Limitación?* "PAM aún acepta usuarios locales como respaldo; en producción se quitaría y se limitaría la VPN al grupo `VPN-Users`."

## Si falla
| Síntoma | Acción |
|---|---|
| Rechaza clave | Clave y código pegados; el código vence a los 30 s, esperar el siguiente |
| Sigue rechazando | Escobar: `az vm run-command invoke … SRVD1DEVVPN001 … "tail -n 20 /var/log/auth.log"` (🟢) y buscar `vendedor1`, `pam_google_authenticator`, `pam_sss` |
| Timeout sin pedir credenciales | VPN001 apagada |
| Connected pero sin 10.1.100.100 | Repetir Test-NetConnection; posible regla de red |
| ERP no carga | Línea en `hosts` de VEN001; 502 = ERP001/SQL001 apagadas |

**Calificación realista:** con pasos 1-5 → mínimo 2,5. El paso 6 sin ensayo es el riesgo.
