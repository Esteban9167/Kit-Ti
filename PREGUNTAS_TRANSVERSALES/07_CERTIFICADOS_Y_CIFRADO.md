# 07 · Certificados y cifrado

[⬅ README](../README.md) · [LBR001](../DANIEL/04_CERTIFICADOS_HTTPS.md) · [PKI001](../NICOLAS/03_PKI_CERTIFICADOS.md)

| Tramo | Cifrado | Responsable |
|---|---|---|
| Usuario → VPN | OpenVPN (TLS) UDP 1194 | Escobar |
| Navegador → LBR001 | HTTPS, Let's Encrypt comodín, hasta 15-feb-2027 | Daniel |
| LBR001 → Odoo/Moodle/Drupal | **HTTP interno** (red privada) | Daniel |
| Webmail | TLS en HTTPSWBM001 | Pulido |
| Admin → servidores | SSH | Esteban |
| Agentes → Wazuh | Canal cifrado del protocolo Wazuh (TCP 1514) | Esteban |
| Servicios internos | step-ca (emisión de prueba ✅; distribución ⏳) | Nicolás |
| Redmine | HTTP interno ⚠️ | Nicolás |
| LDAP Redmine → AD | 389 sin TLS respondió; **LDAPS falló** | Nicolás |

### PREGUNTA CRT1 · ¿Dónde termina TLS?
**🗣️ ORAL:** "Para las aplicaciones web, en LBR001; de ahí al backend va HTTP por la red privada, que no tiene IP pública y está filtrada por NSG. Mejora futura: re-cifrar al backend con certificados de nuestra PKI interna."

### PREGUNTA CRT2 · ¿Por qué dos CA?
Pública (Let's Encrypt) para lo que ven navegadores cualquiera; interna (step-ca) para servicios privados con confianza controlada.

### PREGUNTA CRT3 · ¿Qué pasa si el certificado vence?
Cron diario renueva cuando faltan < 30 días (DNS-01 en DuckDNS). Si falla la renovación: advertencia en navegadores a los 90 días; vence 15-feb-2027.
