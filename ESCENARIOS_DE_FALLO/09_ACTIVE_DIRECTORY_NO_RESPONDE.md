# 09 · Active Directory no responde

[⬅ Plan B](../07_PLAN_B_SI_ALGO_FALLA.md) · [AD](../NICOLAS/01_ACTIVE_DIRECTORY.md)

| Paso | Dónde | Comando 🟢 | Interpretación |
|---|---|---|---|
| 1 | `[AZURE PORTAL]` | Estado de SRVD1DEVAD001 | Windows tarda más en arrancar |
| 2 | `[BASTION]` | `timeout 5 bash -c 'echo > /dev/tcp/10.1.100.100/3389' && echo OK` | Red/RDP |
| 3 | `[BASTION]` | `timeout 5 bash -c 'echo > /dev/tcp/10.1.100.100/389' && echo LDAP` | Servicio AD |
| 4 | `[VEN001/POWERSHELL]` con VPN | `Test-NetConnection 10.1.100.100 -Port 389` | Desde cliente |
| 5 | `[DKR001]` | `curl -sG --data-urlencode 'query=up{job="ad001"}' http://127.0.0.1:9090/api/v1/query` | Exporter |
**Impacto:** VPN no autentica (sssd), RDP de dominio falla. **NO** reiniciar servicios de AD ni el DC en vivo.
