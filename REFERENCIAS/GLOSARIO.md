# Glosario

[⬅ README](../README.md)

| Término | Definición breve |
|---|---|
| AD DS | Servicios de dominio de Active Directory (identidad, Kerberos, LDAP, GPO). |
| Bastión | Servidor de salto único para administración (SSH/RDP). |
| CONNECT | Método HTTP con el que un navegador pide al proxy un túnel TLS a un sitio. |
| DNS-01 | Desafío ACME que prueba control de dominio con un registro TXT. |
| DORA | Discover, Offer, Request, Ack (DHCP). |
| Exporter | Proceso que publica métricas para Prometheus (node 9100, windows 9182). |
| GPO | Objeto de directiva de grupo; se vincula a OU; se aplica en equipos/usuarios del alcance. |
| Kerberos | Protocolo de autenticación por tickets; sensible al desfase de hora (5 min). |
| LDAP / LDAPS | Consulta de directorio (389) / sobre TLS (636). |
| MediaMTX | Servidor RTSP/WebRTC usado como NVR. |
| NAT | Traducción de direcciones; la VPN presenta a los clientes como 10.1.110.10. |
| NOC | Centro de operaciones de red: disponibilidad y rendimiento. |
| NSG | Network Security Group de Azure; reglas por prioridad (menor = primero). |
| OIDC / SAML | Protocolos de federación para SSO. |
| PAM | Módulos de autenticación de Linux (TOTP + sssd en la VPN). |
| PDC Emulator | Rol FSMO del AD que es la fuente de hora del dominio. |
| Peering | Conexión entre VNets; **no transitivo**. |
| PHC0 | Reloj de hardware del hipervisor de Azure usado por chrony. |
| PromQL | Lenguaje de consulta de Prometheus. |
| RPO / RTO | Datos máximos perdidos / tiempo máximo de recuperación. |
| RTSP | Protocolo de streaming de video (puerto 8554 aquí). |
| SIEM | Recolección y correlación de eventos de seguridad (Wazuh). |
| SSO | Inicio de sesión único: autenticarse una vez para varias aplicaciones. |
| sssd | Demonio Linux que consulta identidades del AD. |
| step-ca | CA interna (Smallstep). |
| TOTP | Código de un solo uso basado en tiempo (30 s). |
| `unless-stopped` | Política Docker: reinicia el contenedor salvo que se haya detenido manualmente. |
| UP=1 | El último scrape de Prometheus fue exitoso. |
