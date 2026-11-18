# 06 · Comandos maestros (copiar y pegar)

[⬅ README](README.md) · [Acceso](02_RUTA_DE_ACCESO_A_SERVIDORES.md) · [Plan B](07_PLAN_B_SI_ALGO_FALLA.md)

Formato: **etiqueta de máquina** · riesgo 🟢/🟡/🔴 · origen (✔ = comando con resultado documentado por el equipo · ◇ = comando sugerido, sin evidencia de ejecución).

> Antes de pegar en un servidor: `hostname` 🟢. Si el prompt no coincide con la etiqueta, **no pegues**.

---

## [MAC] / [WINDOWS PC]

| Para | Comando | Riesgo | Origen |
|---|---|---|---|
| Bastión | `ssh bastion` / `ssh USUARIO@10.1.120.10` | 🟢 | ✔ |
| DKR001 | `ssh dkr1` / `ssh -J USUARIO@10.1.120.10 ssdeti@10.1.100.41` | 🟢 | ✔ |
| DKR002 | `ssh dkr2` / `ssh -J USUARIO@10.1.120.10 ssdeti@10.1.100.102` | 🟢 | ✔ |
| SIEM001 | `ssh siem` / `ssh -J USUARIO@10.1.120.10 ssdeti@10.1.100.101` | 🟢 | ✔ |
| ERP001 | `ssh -J bastion ssdeti@10.1.100.70` | 🟢 | ✔ (consolidado) |
| SQL001 | `ssh -J bastion ssdeti@10.1.100.71` | 🟢 | ✔ (estructura) |
| Túnel Grafana | `ssh -N -L 3000:127.0.0.1:3000 dkr1` | 🟡 (abre puerto local) | ✔ |
| Túnel Prometheus UI | `ssh -N -L 9090:127.0.0.1:9090 dkr1` → http://localhost:9090 | 🟡 | ◇ |
| Túnel RDP AD001 | `ssh -o ExitOnForwardFailure=yes -N -L 13389:10.1.100.100:3389 bastion` | 🟡 | ✔ |
| Probar túnel RDP | `nc -vz 127.0.0.1 13389` → `succeeded` | 🟢 | ✔ |
| Traer grabación NVR | `scp dkr2:/opt/langustino-nvr/recordings/camara1_prueba.mp4 ~/Downloads/` | 🟡 (crea archivo local) | ✔ |
| Reproducir | `open ~/Downloads/camara1_prueba.mp4` | 🟢 | ✔ |

## [BASTION]

```bash
sudo sshd -t && echo "Configuración SSH válida"                                   # 🟢 ✔
sudo sshd -T | grep -E "^(pubkeyauthentication|passwordauthentication|permitrootlogin|allowusers|allowtcpforwarding|gatewayports) "   # 🟢 ✔
sudo ls -ld /home/nicolas /home/nicolas/.ssh /home/nicolas/.ssh/authorized_keys   # 🟢 ✔ (permisos, NO contenido)
timeout 5 bash -c 'echo > /dev/tcp/10.1.100.100/3389' && echo 'RDP accesible'      # 🟢 ✔
chronyc sources -v ; chronyc tracking                                               # 🟢 ✔
cat /etc/chrony/sources.d/langustino.sources                                        # 🟢 ✔
sudo tail -n 20 /var/log/auth.log                                                    # 🟢 ◇ (accesos SSH)
```
Carga de CPU para la prueba 6 (🟡 temporal, se detiene sola a los 60 s) ✔ (consolidado):
```bash
for i in 1 2; do timeout 60 nice -n 19 yes > /dev/null & done; wait
```

## [DKR001] (10.1.100.41)

```bash
sudo docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}"                 # 🟢 ✔
# NOC
curl -sG --data-urlencode 'query=up' http://127.0.0.1:9090/api/v1/query | python3 -m json.tool          # 🟢 ✔
curl -sG --data-urlencode 'query=up{job="ad001"}' http://127.0.0.1:9090/api/v1/query                    # 🟢 ✔
sudo docker inspect --format '{{.Name}}: {{.HostConfig.RestartPolicy.Name}}' SRVD1DEVNOC001-prometheus SRVD1DEVNOC001-grafana   # 🟢 ✔
# Proxy filtro (Pulido)
curl -s -o /dev/null -w "%{http_code}\n" -x http://10.1.100.41:3128 https://www.google.com           # 🟢 ✔ → 200
curl -v -x http://10.1.100.41:3128 https://www.facebook.com -o /dev/null 2>&1 | grep -E "HTTP/|CONNECT|403"   # 🟢 ✔ → 403
sudo docker exec SRVD1DEVPRX001 sh -c "tail -n 30 /var/log/squid/access.log" | grep -E "DENIED|TUNNEL"  # 🟢 ✔
# DNS (Pulido)
dig @10.1.100.41 mail.langustino-lab.duckdns.org +short                              # 🟢 ✔ → 10.1.100.102
dig @10.1.100.41 google.com A +short                                                  # 🟢 ✔
dig @10.1.100.41 -p 5353 langustino-lab.duckdns.org A +short                         # 🟢 ✔ → 172.212.220.169
sudo docker exec SRVD1DEVDNS001 named-checkconf                                       # 🟢 ✔
# DHCP (Pulido) — 🟡 renueva lease del cliente de laboratorio aislado
sudo docker exec langustino-cliente-dhcp sh -c 'dhclient -r eth0; dhclient -1 -v eth0'   # ✔ DORA → 172.30.50.100
# PKI (Nicolás)
sudo docker ps --filter name=SRVD1DEVPKI001                                            # 🟢 ✔
curl --fail --silent --show-error --cacert "$HOME/langustino/pki001/root_ca.crt" https://10.1.100.41:9000/health   # 🟢 ✔ → {"status":"ok"}
openssl x509 -in "$HOME/langustino/pki001/root_ca.crt" -noout -subject -issuer -dates # 🟢 ✔
# Samba (Nicolás) — la clave se escribe cuando la pida, nunca en el comando
smbclient //10.1.100.41/software -U soporte1 -c "ls"                                  # 🟢 ✔ lista
smbclient //10.1.100.41/software -U operario1 -c "ls"                                 # 🟢 ✔ NT_STATUS_ACCESS_DENIED
# Backups (Nicolás)
crontab -l | grep -E "backup-(fil|msa)"                                               # 🟢 ✔
tail -n 12 ~/langustino/bkp001/backup-msa.log                                         # 🟢 ✔ "snapshot saved"
tail -n 12 ~/langustino/bkp001/backup-fil.log                                         # 🟢 ✔
# NTP servidor
sudo docker ps --filter name=SRVD1DEVNTP001                                           # 🟢 ◇
sudo ss -ulpn | grep ':123 '                                                          # 🟢 ◇
```

## [DKR002] (10.1.100.102)

```bash
sudo docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}"                 # 🟢 ✔
# NVR (Esteban)
sudo docker ps --filter "name=SRVD1DEVNVR001" --filter "name=SRVD1DEVCAM001"         # 🟢 ✔
sudo docker logs --tail 25 SRVD1DEVNVR001                                             # 🟢 ✔ "stream is available and online, 1 track (H264)"
ls -lh /opt/langustino-nvr/recordings/                                                # 🟢 ✔
sudo docker run --rm --entrypoint ffprobe -v /opt/langustino-nvr/recordings:/recordings:ro linuxserver/ffmpeg:latest -v error -show_entries format=duration:stream=codec_name,width,height -of default=noprint_wrappers=1 /recordings/camara1_prueba.mp4   # 🟢 ✔ (contenedor temporal --rm, solo lectura)
# NTP cliente
chronyc sources -v ; chronyc tracking                                                  # 🟢 ✔
sudo chronyd -Q -t 15 'server 10.1.100.41 iburst'                                     # 🟢 ✔ (consulta, no ajusta)
# Correo (Pulido)
sudo docker logs SRVD1DEVMTA001 --since 10m | grep -iE "reject|554"                   # 🟢 ◇ (consolidado)
sudo docker exec SRVD1DEVMTA001 postqueue -p                                          # 🟢 ◇
# Redmine (Nicolás)
cd ~/langustino/msa001 && docker compose -f compose.yml ps                            # 🟢 ✔
```

## [SIEM001] (10.1.100.101)

```bash
sudo /var/ossec/bin/agent_control -l                                                  # 🟢 ✔ 000-003 Active
sudo tail -n 3 /var/ossec/logs/alerts/alerts.json                                     # 🟢 ✔
sudo wc -l /var/ossec/logs/alerts/alerts.json                                         # 🟢 ✔
sudo grep -o '"agent":{"id":"[0-9]*","name":"[^"]*"' /var/ossec/logs/alerts/alerts.json | sort | uniq -c   # 🟢 ✔ (conteo histórico por agente)
chronyc sources -v                                                                     # 🟢 ✔
sudo systemctl status wazuh-manager --no-pager                                         # 🟢 ◇
```
Conteo de alertas de los últimos 30 min por agente (🟢 ✔, versión corregida del timestamp):
```bash
sudo python3 - <<'PY'
import json
from datetime import datetime, timedelta, timezone
from collections import Counter
limite = datetime.now(timezone.utc) - timedelta(minutes=30)
c = Counter()
with open('/var/ossec/logs/alerts/alerts.json') as f:
    for linea in f:
        try:
            e = json.loads(linea)
            fecha = datetime.strptime(e['timestamp'], '%Y-%m-%dT%H:%M:%S.%f%z')
            if fecha >= limite:
                c[e.get('agent', {}).get('name', 'Desconocido')] += 1
        except (ValueError, KeyError, json.JSONDecodeError):
            pass
for nombre, total in sorted(c.items()):
    print(nombre, total)
PY
```

## [AD001/POWERSHELL] (solo con RDP autorizado)

```powershell
$env:COMPUTERNAME                                                                       # 🟢 ✔
Import-Module ActiveDirectory
(Get-ADDomain) | Select-Object DNSRoot,NetBIOSName,DomainMode                           # 🟢 ✔
Get-ADOrganizationalUnit -Filter * | Select-Object Name,DistinguishedName               # 🟢 ✔
Get-ADGroup -Filter "Name -eq 'VPN-Users' -or Name -eq 'Administradores-TI'" | Select-Object Name,GroupScope,DistinguishedName   # 🟢 ✔
Get-ADUser -Filter * | Select-Object Name,Enabled,DistinguishedName                     # 🟢 ✔
Get-ADComputer -Filter * | Select-Object Name,DistinguishedName                         # 🟢 ✔
Import-Module GroupPolicy
Get-GPO -All | Select-Object DisplayName,GpoStatus                                      # 🟢 ✔
Get-GPInheritance -Target "OU=Equipos,DC=langustino,DC=local" | Format-List GpoLinks   # 🟢 ✔
Get-GPRegistryValue -Name "LANgustino-Seguridad-Equipos" -Key "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System" -ValueName "InactivityTimeoutSecs" | Select-Object ValueName,Type,Value   # 🟢 ✔ → 900
(Invoke-WebRequest http://localhost:9182/metrics -UseBasicParsing).StatusCode          # 🟢 ✔ → 200
Test-NetConnection www.google.com -Port 443                                             # 🟢 ◇ → False esperado (sin Internet)
w32tm /query /source ; w32tm /query /status ; netdom query fsmo                         # 🟢 ◇ PENDIENTE (nunca ejecutado)
```

## [VEN001/POWERSHELL]

```powershell
(Get-CimInstance Win32_ComputerSystem) | Select Name, Domain, PartOfDomain             # 🟢 ✔ langustino.local / True
ipconfig                                                                                 # 🟢 ✔ 192.168.50.4 (sin VPN) / 10.8.0.x (con VPN)
Test-NetConnection 10.1.100.100 -Port 389                                               # 🟢 ✔ False sin VPN / True con VPN
```

## [CLOUD SHELL] / [AZURE PORTAL]

```bash
az vm list -g rg-langustino-dev -d --query "[].{VM:name,Region:location,Tamano:hardwareProfile.vmSize,IP:privateIps,Publica:publicIps}" -o table   # 🟢 ✔
az network nsg rule list -g rg-langustino-dev --nsg-name nsg-langustino-r3 -o table   # 🟢 ◇ (cambiar nsg-langustino-cu / -nc / -vnd)
az vm run-command invoke -g rg-langustino-dev -n SRVD1DEVVPN001 --command-id RunShellScript --scripts "tail -n 20 /var/log/auth.log" --query "value[0].message" -o tsv   # 🟢 ✔ (lee log; Escobar)
```

## 🔴 NO EJECUTAR EN VIVO (solo preparación previa, responsable indicado)

| Comando | Responsable | Por qué es 🔴 |
|---|---|---|
| `az network nsg rule create … -n Allow-VPN-a-PRX001-3128 --priority 129 …` | Escobar | Cambia NSG |
| `az network nsg rule update … nsg-langustino-vnd -n Allow-RDP-Santiago …` | Escobar | Cambia NSG |
| `az vm auto-shutdown -g rg-langustino-dev -n SRVD1DEVVEN001 --time 0400 --location brazilsouth` | Escobar | Cambia VM |
| `Set-ItemProperty … Internet Settings … ProxyServer/ProxyEnable` en VEN001 | Escobar | Cambia configuración de cliente |
| Cualquier `Set-GP*`, `New-AD*`, `Move-ADObject`, cambio de DNS | Nicolás | Dominio |
| `docker restart` de contenedores | Dueño | Cambia estado (solo si escenario de fallo lo exige) |
| Script de restauración BKP001 | Nicolás | Crea contenedor temporal (aislado, `--network none`) — solo con tiempo |
