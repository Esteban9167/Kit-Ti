# 02 · Red y conectividad

[⬅ README](../README.md) · [Enrutamiento](../SANTIAGO/03_ENRUTAMIENTO_CONECTIVIDAD.md)

### PREGUNTA RED1 · ¿Qué servicios dependen de DNS?
**🗣️ ORAL:** "El dominio depende del DNS del AD: unión de VEN001 y VPN001, Kerberos y sssd de la VPN. Los nombres web dependen de DuckDNS desde Internet y del archivo hosts o DNS001 desde la VPN. El correo usa el registro mail interno. Y DNS001 reenvía lo externo a Azure DNS."
| Servicio | Depende de | Integrante |
|---|---|---|
| VPN (sssd → AD) | DNS de AD001 | Escobar / Nicolás |
| VEN001 en dominio | DNS de AD001 | Escobar / Nicolás |
| ERP/CMS por VPN | `hosts` → 10.1.100.41 (o DNS001) | Daniel / Pulido |
| LMS público | DuckDNS → 172.212.220.169 | Daniel |
| Correo | `mail` en DNS001 | Pulido |
| LDAPS Redmine → AD | FQDN del DC (**no resuelve desde DKR002**) | Nicolás |
| Renovación certificado | TXT DuckDNS (DNS-01) | Daniel |

### PREGUNTA RED2 · ¿Cómo demuestro que dos servidores se comunican?
Puerto concreto, no ping. Ejemplos 🟢: `[BASTION]` `/dev/tcp/10.1.100.100/3389`; `[DKR001]` `up` de Prometheus (DKR001→todos); `[VEN001]` `Test-NetConnection 10.1.100.100 -Port 389`.

### PREGUNTA RED3 · ¿El peering es transitivo?
No. Por eso malla completa y reglas por cada flujo inter-región.

### PREGUNTA RED4 · ¿Qué hace la regla 4000?
Deny VirtualNetwork→VirtualNetwork; anula la regla por defecto 65000 que permite todo dentro de la VNet/peerings.
