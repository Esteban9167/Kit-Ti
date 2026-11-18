# 02 · Ruta de acceso a servidores (paso a paso, sin pensar)

[⬅ README](README.md) · [Inventario](03_INVENTARIO_SERVIDORES_IP_PUERTOS.md) · [Fallo SSH](ESCENARIOS_DE_FALLO/02_SSH_NO_CONECTA.md) · [Fallo VPN](ESCENARIOS_DE_FALLO/03_VPN_NO_CONECTA.md)

## Mapa mental

```text
TU MAC / PC ──(OpenVPN UDP 1194, usuario AD + TOTP)──► VPN001 10.1.110.10 ──► red privada 10.1.0.0/16
     │
     └─ ssh ─► BASTIÓN SRVD1DEVBAS001 10.1.120.10 (sin IP pública, único origen permitido de SSH 22 y RDP 3389)
                 ├─ ssh ─► DKR001 10.1.100.41   (dkr1)
                 ├─ ssh ─► DKR002 10.1.100.102  (dkr2)
                 ├─ ssh ─► SIEM001 10.1.100.101 (siem)
                 ├─ ssh ─► ERP001 10.1.100.70 · SQL001 10.1.100.71 · PXY001 10.1.100.40
                 └─ túnel ─► AD001 10.1.100.100:3389 (RDP)
```

Usuario en los servidores Linux: **`ssdeti`** (según guías del equipo). Usuarios autorizados en el bastión (`AllowUsers`): `daniel esteban spulido escobar nicolas`. Autenticación **solo por llave pública** (`PasswordAuthentication no`, `PermitRootLogin no`).

---

## Paso 0 — Activar la VPN (siempre primero)

| # | Dónde | Acción | Qué debes ver |
|---|---|---|---|
| 1 | `[MAC]` / `[WINDOWS PC]` | Abrir **OpenVPN Connect** → tu perfil personal → Connect | Pide usuario y contraseña |
| 2 | ídem | Contraseña = **clave AD + 6 dígitos TOTP, pegados, sin espacio** | Estado **Connected** |
| 3 | `[MAC]` terminal | `ping -c 2 10.1.120.10` (🟢) | Responde (ICMP permitido dentro de VirtualNetwork; si el ping falla pero SSH funciona, no es problema) |

Si falla → [ESCENARIOS_DE_FALLO/03_VPN_NO_CONECTA.md](ESCENARIOS_DE_FALLO/03_VPN_NO_CONECTA.md).

---

## Ruta A — Desde el MAC de Esteban (alias ya configurados)

Esteban tiene en `~/.ssh/config` los alias **`bastion`, `dkr1`, `dkr2`, `siem`** (confirmado en guía de Esteban y consolidado).

Abre **Terminal** (o iTerm). Una pestaña por servidor:

| Pestaña | `[MAC]` comando | Prompt que debes reconocer | Para |
|---|---|---|---|
| 1 | `ssh bastion` | `…@SRVD1DEVBAS001` (o hostname del bastión) | sshd, chrony, túnel |
| 2 | `ssh dkr1` | `ssdeti@SRVD1DEVDKR001` | Prometheus, Grafana, NTP, proxy, DNS, PKI, Samba, backups, LBR, LMS |
| 3 | `ssh dkr2` | `ssdeti@SRVD1DEVDKR002` | NVR, PBX/IVR, correo, Redmine, CMS |
| 4 | `ssh siem` | `…@SRVD1DEVSIE001` | Wazuh |
| 5 | `ssh -N -L 3000:127.0.0.1:3000 dkr1` | **No muestra nada** (es normal, queda abierto) | Grafana en http://localhost:3000 |

Servidores sin alias (ERP, SQL, PXY): 🟢
```bash
# [MAC]
ssh -J bastion ssdeti@10.1.100.70    # ERP001
ssh -J bastion ssdeti@10.1.100.71    # SQL001
ssh -J bastion ssdeti@10.1.100.40    # PXY001
```

**Confirmar SIEMPRE en qué máquina estás antes de pegar:** 🟢
```bash
hostname; date -Is
```

> Si los alias no existieran en otro Mac, plantilla para `~/.ssh/config` (**ajustar `TU_USUARIO`**; no fue copiada del Mac de Esteban):
> ```text
> Host bastion
>   HostName 10.1.120.10
>   User TU_USUARIO
> Host dkr1
>   HostName 10.1.100.41
>   User ssdeti
>   ProxyJump bastion
> Host dkr2
>   HostName 10.1.100.102
>   User ssdeti
>   ProxyJump bastion
> Host siem
>   HostName 10.1.100.101
>   User ssdeti
>   ProxyJump bastion
> ```

---

## Ruta B — Desde un PC Windows (compañeros sin alias)

Abrir **PowerShell** (no hace falta admin para SSH). Reemplazar `USUARIO` por tu usuario del bastión (`daniel`, `spulido`, `escobar`, `nicolas`, `esteban`).

```powershell
# [WINDOWS PC]  🟢
ssh USUARIO@10.1.120.10                               # Bastión
ssh -J USUARIO@10.1.120.10 ssdeti@10.1.100.41         # DKR001
ssh -J USUARIO@10.1.120.10 ssdeti@10.1.100.102        # DKR002
ssh -J USUARIO@10.1.120.10 ssdeti@10.1.100.101        # SIEM001
ssh -J USUARIO@10.1.120.10 ssdeti@10.1.100.70         # ERP001
ssh -J USUARIO@10.1.120.10 ssdeti@10.1.100.71         # SQL001
```

Archivo `hosts` (una sola vez, **PowerShell como administrador**) 🟡 — necesario para ERP y CMS por VPN:
```powershell
Add-Content -Path C:\Windows\System32\drivers\etc\hosts -Value "`n10.1.100.41 erp.langustino-lab.duckdns.org cms.langustino-lab.duckdns.org"
ipconfig /flushdns
```
En Mac el equivalente sería `sudo sh -c 'echo "10.1.100.41 erp.langustino-lab.duckdns.org cms.langustino-lab.duckdns.org" >> /etc/hosts'` 🟡 (no documentado como ejecutado por el equipo).

---

## Ruta C — Grafana (túnel)

| # | Dónde | Comando / acción |
|---|---|---|
| 1 | `[MAC]` | `ssh -N -L 3000:127.0.0.1:3000 dkr1` (dejar abierta) |
| 1' | `[WINDOWS PC]` | `ssh -N -L 3000:127.0.0.1:3000 -J USUARIO@10.1.120.10 ssdeti@10.1.100.41` |
| 2 | `[NAVEGADOR/GRAFANA]` | http://localhost:3000 → usuario del gestor |
| 3 | `[NAVEGADOR/GRAFANA]` | Dashboards → **LANgustino - Monitoreo NOC** (Linux) · **Windows Exporter Dashboard 2025 (v0.31+ compatible)** (AD001) |
| 4 | Salir | `Ctrl+C` en la terminal del túnel |

Grafana (3000), Prometheus (9090) y BKP001 (8000) **solo escuchan en localhost de DKR001**: por eso el túnel.
`bind: Address already in use` → ya hay un túnel abierto: reutilízalo o usa `-L 3001:127.0.0.1:3000` y abre http://localhost:3001.

---

## Ruta D — RDP a AD001 (túnel por el bastión)

| # | Dónde | Comando / acción | Qué ver |
|---|---|---|---|
| 1 | `[MAC]` terminal 1 | `ssh -o ExitOnForwardFailure=yes -N -L 13389:10.1.100.100:3389 bastion` | Nada (queda abierto) |
| 1' | `[WINDOWS PC]` ventana 1 | `ssh -o ExitOnForwardFailure=yes -N -L 13389:10.1.100.100:3389 USUARIO@10.1.120.10` | Nada |
| 2 | `[MAC]` terminal 2 | `nc -vz 127.0.0.1 13389` 🟢 | `succeeded` ✅ (verificado por Esteban) |
| 3 | `[MAC]` | App **Windows App** → PC `localhost:13389` | Pantalla de login de Windows |
| 3' | `[WINDOWS PC]` | `mstsc /v:localhost:13389` | ídem |
| 4 | Login | `LANGUSTINO\ssdeti` + clave del gestor | Escritorio SRVD1DEVAD001 |
| 5 | Salir | Cerrar sesión RDP → `Ctrl+C` en terminal 1 | |

⚠️ Esteban verificó el túnel (TCP) pero **no** inició sesión Windows autenticada (no tenía credenciales autorizadas). Nicolás y el consolidado reportan RDP al AD funcionando. **No adivinar contraseñas.** Si el puerto 13389 está ocupado, usar 13390.

---

## Ruta E — VM del vendedor (SRVD1DEVVEN001, Brazil South)

| # | Dónde | Acción |
|---|---|---|
| 0 | `[CLOUD SHELL]` (Escobar, **antes**, 🔴) | Solo si otra persona va a entrar: agregar su IP pública a la regla `Allow-RDP-Santiago` de `nsg-langustino-vnd` (ver [SANTIAGO/04_FIREWALL_SEGURIDAD.md](SANTIAGO/04_FIREWALL_SEGURIDAD.md)) |
| 1 | `[WINDOWS PC]` | `mstsc /v:20.226.80.153` · usuario **`.\ssdeti`** (punto y barra = cuenta local) · clave del gestor |
| 1' | `[MAC]` | Windows App → PC `20.226.80.153` · usuario `.\ssdeti` |
| 2 | `[VEN001/POWERSHELL]` | `(Get-CimInstance Win32_ComputerSystem) \| Select Name, Domain, PartOfDomain` 🟢 |

Solo el PC de **Santiago Escobar** está permitido hoy por NSG. Por eso las pruebas 2, 4, 5 y 8 se recomiendan desde su PC.

---

## Ruta F — Azure Portal / Cloud Shell

| # | Dónde | Acción |
|---|---|---|
| 1 | Navegador | https://portal.azure.com → grupo de recursos **rg-langustino-dev** |
| 2 | `[AZURE PORTAL]` | Máquinas virtuales → seleccionar VM → **Información general** (tamaño, IP, SO, región) · **Redes** (NSG, subred) |
| 3 | `[CLOUD SHELL]` | Inventario en vivo 🟢: `az vm list -g rg-langustino-dev -d --query "[].{VM:name,Region:location,Tamano:hardwareProfile.vmSize,IP:privateIps,Publica:publicIps}" -o table` |

---

## Cómo salir sin dejar procesos peligrosos

| Situación | Acción |
|---|---|
| Sesión SSH normal | `exit` |
| Túnel `-N` | `Ctrl+C` en esa terminal |
| `docker logs -f` / `tail -f` | `Ctrl+C` (no detiene el contenedor) |
| Prueba de CPU `yes` | Usa siempre `timeout 60`; si algo queda: `pkill -f "yes"` 🟡 en ese mismo servidor |
| Sesión RDP | **Cerrar sesión** (no solo cerrar ventana) |
| VPN | Dejar conectada hasta terminar |
