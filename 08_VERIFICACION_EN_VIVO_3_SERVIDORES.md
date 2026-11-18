# 08 · Verificación en vivo de 3 servidores (criterio 2 · 6 min)

[⬅ README](README.md) · [Inventario](03_INVENTARIO_SERVIDORES_IP_PUERTOS.md)

La profesora elige 3 servidores y contrasta: **recursos (vCPU, RAM, disco), subred e IP, SO y rol**. También revisa si hay recursos desplegados que no estén en el diagrama. Para nivel 5: **cualquier integrante verifica solo**, con consola o comandos.

## Método en 2 minutos por servidor

**Opción 1 — Portal (más rápido, no requiere VPN)** 🟢
1. `[AZURE PORTAL]` → rg-langustino-dev → Máquinas virtuales → clic en la VM.
2. **Información general**: Tamaño (p. ej. *Standard B2als v2 · 2 vCPU · 4 GiB*), Ubicación (región), SO, IP privada/pública.
3. **Redes**: subred y NSG. **Discos**: tamaño del disco del SO.
4. Señalar en el diagrama el mismo nodo.

**Opción 2 — Cloud Shell (una sola tabla para todos)** 🟢
```bash
# [CLOUD SHELL]
az vm list -g rg-langustino-dev -d --query "[].{VM:name,Region:location,Tamano:hardwareProfile.vmSize,IP:privateIps,Publica:publicIps}" -o table
```

**Opción 3 — Desde dentro (Linux)** 🟢
```bash
# [DKR001] / [DKR002] / [SIEM001] / [BASTION] / ERP001 / SQL001
hostname; hostname -I; nproc; free -h; lsblk -d -o NAME,SIZE; df -h /; lsb_release -d; uname -m
```
`uname -m` → `aarch64` en VPN001, PXY001 y DKR001 (ARM64); `x86_64` en las demás Linux.

**Opción 3b — Desde dentro (Windows AD001)** 🟢
```powershell
# [AD001/POWERSHELL]
$env:COMPUTERNAME; (Get-CimInstance Win32_ComputerSystem).NumberOfLogicalProcessors
[math]::Round((Get-CimInstance Win32_ComputerSystem).TotalPhysicalMemory/1GB,1)
Get-CimInstance Win32_LogicalDisk -Filter "DeviceID='C:'" | Select Size,FreeSpace
(Get-CimInstance Win32_OperatingSystem).Caption; Get-NetIPAddress -AddressFamily IPv4 | Select IPAddress
```

## Valores esperados (deben coincidir con el diagrama)

| VM | Tamaño | vCPU | RAM | Disco | IP | Región / subred | SO | Rol | Comprobación de rol 🟢 |
|---|---|---|---|---|---|---|---|---|---|
| VPN001 | B2pts_v2 | 2 | 1 GiB | 32 GB | 10.1.110.10 / 52.154.177.199 | Central US / snet-dmz | Ubuntu 22.04 ARM64 | OpenVPN | Conectar la VPN y mostrar IP 10.8.0.x |
| PXY001 | B2pts_v2 | 2 | 1 GiB | 32 GB | 10.1.100.40 | Central US / snet-proxy | Ubuntu 22.04 ARM64 | Squid salida | `ssh -J bastion ssdeti@10.1.100.40` → `systemctl status squid` ◇ |
| DKR001 | B2pls_v2 | 2 | 4 GiB | 64 GB | 10.1.100.41 / 172.212.220.169 | Central US / snet-proxy | Ubuntu 22.04 ARM64 | Host Docker, 12 contenedores | `sudo docker ps` |
| BAS001 | B2ats_v2 | 2 | 1 GiB | 32 GB | 10.1.120.10 | North Central US / snet-gestion | Ubuntu 22.04 x64 | Bastión | `sudo sshd -T \| grep allowusers` |
| ERP001 | B2als_v2 | 2 | 4 GiB | 32 GB | 10.1.100.70 | North Central US / snet-erp | Ubuntu 22.04 x64 | Odoo 17 | `sudo ss -lntp \| grep 8069` ◇ |
| SQL001 | B2als_v2 | 2 | 4 GiB | 32 GB | 10.1.100.71 | North Central US / snet-erp | Ubuntu 22.04 x64 | PostgreSQL 14 | `sudo -u postgres psql -c '\l'` ◇ (bases langustino, lms, cms, msa) |
| AD001 | B2als_v2 | 2 | 4 GiB | 127 GB | 10.1.100.100 | Chile Central / snet-servicios | Windows Server 2022 | AD DS + DNS | `(Get-ADDomain).DNSRoot` |
| SIE001 | B2als_v2 | 2 | 4 GiB | 32 GB | 10.1.100.101 | Chile Central / snet-servicios | Ubuntu 22.04 x64 | Wazuh | `sudo /var/ossec/bin/agent_control -l` |
| DKR002 | B2as_v2 | 2 | 8 GiB | 64 GB | 10.1.100.102 | Chile Central / snet-servicios | Ubuntu 22.04 x64 | Host Docker, 9 contenedores | `sudo docker ps` |
| VEN001 | D2s_v4 | 2 | 8 GiB | 127 GB | 192.168.50.4 / 20.226.80.153 | Brazil South / snet-vendor | Windows Server 2022 | Vendedor | `PartOfDomain True` |

## Diferencias conocidas que hay que explicar ANTES de que las encuentre (sube el nivel)

| Diferencia | Explicación |
|---|---|
| `free -h` muestra algo menos que 4/8 GiB | Memoria reservada por el kernel/hipervisor; el tamaño Azure es el nominal |
| `df -h` muestra ~62 GB en un disco de 64 GB | Particiones y sistema de archivos |
| DKR002 tiene contenedores adicionales detenidos | "Respaldos detenidos del NVR y contenedores de prueba sin rol, fuera de la demo" (nota del diagrama) |
| DKR001 tiene `langustino-cliente-dhcp` | Cliente de laboratorio DHCP, no es servidor de rol |
| Dos Squid (PXY001 y PRX001) | PXY001 = salida a Internet; PRX001 = filtro de navegación para empleados |
| Discos Standard HDD | Decisión de costo, anotada en el diagrama |

## Frase de cierre
"Los valores coinciden con el diagrama; el inventario del diagrama se sacó directamente de `az vm list` y del portal."
