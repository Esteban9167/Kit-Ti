# 03 · Enrutamiento y conectividad

[⬅ Índice Santiago](00_INDICE_SANTIAGO.md) · [Red (transversal)](../PREGUNTAS_TRANSVERSALES/02_RED_Y_CONECTIVIDAD.md)

| Tema | Cómo funciona |
|---|---|
| Peering | En **malla completa** entre cu, nc y r3 (no transitivo → cada par tiene su peering) |
| VNet vendedor | **Sin peering**; VEN001 solo llega por VPN |
| VPN | Clientes 10.8.0.0/24; **NAT** a 10.1.110.10 → por eso las NSG y el proxy ven 10.1.110.10 |
| Salida a Internet de subredes privadas | Por **PXY001** (pip-proxy-salida) |
| Excepción | **DKR001 sale directo** (tiene pip-lbr-dkr001) — declarada en el diagrama |
| DNS | Azure DNS 168.63.129.16 (reenviador de DNS001); `langustino.local` en AD001 |
| DHCP | Azure asigna IP de VM; Kea solo en red Docker aislada (Azure no propaga broadcast) |

### PREGUNTA R1 · ¿Cómo demuestro que un servidor se comunica con otro?
**🗣️ ORAL:** "Con una conexión al puerto concreto, no con ping: por ejemplo desde el bastión a AD001 por 3389, o el target UP de Prometheus que prueba DKR001→DKR002:9100."

**⌨️ (🟢):**
```bash
# [BASTION]
timeout 5 bash -c 'echo > /dev/tcp/10.1.100.100/3389' && echo OK
```
```powershell
# [VEN001/POWERSHELL] o [WINDOWS PC] con VPN
Test-NetConnection 10.1.100.100 -Port 389
```

### PREGUNTA R2 · ¿Por qué la VPN hace NAT?
"Para que el tráfico de los clientes salga con la IP de VPN001, que es la que las NSG conocen; así no hay que enrutar 10.8.0.0/24 en Azure. Costo: se pierde la IP individual del usuario en los logs (se correlaciona por hora con el log de la VPN)."
