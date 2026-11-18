# 09 · SRVD1DEVVEN001 — VM del vendedor *(archivo adicional)*

[⬅ Índice Santiago](00_INDICE_SANTIAGO.md) · [P02](../PRUEBAS_RECORRIDO/P02_VENDEDOR_REMOTO.md)

**Ficha:** Brazil South · vnet-langustino-vnd 192.168.50.0/24 · snet-vendor 192.168.50.0/27 · IP 192.168.50.4 · pública 20.226.80.153 · D2s_v4 2/8 GiB/127 GB · Windows Server 2022 · unida a `langustino.local` · OpenVPN Connect · **sin peering**.

**Acceso:** `mstsc /v:20.226.80.153` usuario `.\ssdeti` (cuenta local). Solo desde IP permitida en `nsg-langustino-vnd`.

**Auto-apagado:** había riesgo de apagado a las 6 p. m.; comando de ajuste en [04_FIREWALL_SEGURIDAD.md](04_FIREWALL_SEGURIDAD.md) (🔴, Escobar).

| Comprobación 🟢 `[VEN001/POWERSHELL]` | Esperado |
|---|---|
| `(Get-CimInstance Win32_ComputerSystem) \| Select Name, Domain, PartOfDomain` | langustino.local · True |
| `ipconfig` (sin VPN / con VPN) | 192.168.50.4 / + 10.8.0.x |
| `Test-NetConnection 10.1.100.100 -Port 389` (sin / con VPN) | False / True |

**Pendientes en VEN001:** línea `hosts` para erp/cms, proxy 10.1.100.41:3128, abrir ERP (prueba 2 paso 6), GPO no aplicada (está en CN=Computers).
