# 01 · Seguridad (transversal)

[⬅ README](../README.md) · [NSG](../SANTIAGO/02_VNET_SUBREDES_NSG.md) · [Firewall](../SANTIAGO/04_FIREWALL_SEGURIDAD.md)

### PREGUNTA SEG1 · ¿Qué medidas de seguridad implementaron? (respuesta de 20 s)
"Defensa en capas: dos puertas públicas (VPN con AD + TOTP y nginx 80/443); todo lo demás sin IP pública. NSG con regla 4000 de denegar entre redes y reglas IP→IP:puerto. Administración solo por bastión con llave pública. TLS con Let's Encrypt; PKI interna. Proxy que bloquea 16 dominios. Correo que no entrega fuera. Mínimo privilegio en BD (`ivr_ro`) y Samba solo lectura. Monitoreo y SIEM."

| Capa | Control | Integrante | Servidor | Evidencia |
|---|---|---|---|---|
| Perímetro | 2 IP públicas de servicio; NSG 4000 | Escobar | Azure | Portal / diagrama |
| Acceso remoto | VPN AD + TOTP | Escobar (+Nicolás AD) | VPN001, AD001 | P2 pasos 1-5 ✅ |
| Administración | Bastión endurecido, túneles | Esteban | BAS001 | `sshd -T` ✅ |
| Web | TLS LE, allow/deny 403 | Daniel | LBR001 | P7, P9 ✅ |
| Navegación | Squid 16 dominios | Pulido | PRX001 | curl 403 ✅ |
| Correo | Sin relay externo | Pulido | MTA001 | 554 observado 🟠 |
| Datos | `ivr_ro`, BD fuera de apps, 5432 por app | Daniel | SQL001 | P1 ✅ |
| Archivos | Samba solo lectura | Nicolás | FIL001 | smbclient ✅ |
| Identidad | AD, grupos, GPO | Nicolás | AD001 | 🟠 |
| Detección | Wazuh 4 fuentes, NOC | Esteban | SIE001, DKR001 | ✅ |
| Recuperación | Restic + restauración | Nicolás | DKR001 | ✅ |

### PREGUNTA SEG2 · ¿Cómo están separados los servicios públicos de los privados?
**🗣️ ORAL:** "Solo el LMS es público. ERP e intranet pasan por el mismo nginx pero con allow solo para 10.1.0.0/16; desde Internet dan 403. Los demás servicios no tienen IP pública y solo aceptan orígenes concretos por NSG."

**👥** Daniel (LBR001), Escobar (NSG) · **Evidencia:** P7 paso 3 y P10 paso 6 (403 desde celular).

### PREGUNTA SEG3 · ¿Qué problemas de seguridad reconocen?
1. PAM de la VPN acepta usuarios locales de respaldo. 2. HTTPS sin inspección (filtro por nombre). 3. LDAP/LDAPS y SSO sin terminar. 4. Redmine por HTTP interno. 5. TLS termina en el borde (HTTP interno). 6. Respaldos en el mismo host. 7. DKR001 sale sin proxy. 8. Wazuh sin fuentes de VPN/proxy/nginx/AD. 9. GPO no aplicadas a clientes.

### PREGUNTA SEG4 · ¿Qué NO mostraríamos nunca en pantalla?
Contraseñas, `.env` (restic.env, samba.env, pg-msa.env, rest-server.env), claves privadas de step-ca o SSH, `authorized_keys` (solo permisos), códigos TOTP.
