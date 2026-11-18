# 11 · Riesgos y limitaciones (decirlas antes de que las encuentren)

[⬅ README](../README.md) · [Pendientes reales](../REFERENCIAS/PENDIENTES_REALES.md)

| # | Limitación | Dueño |
|---|---|---|
| 1 | SSO no implementado (prueba 3) | Nicolás + Daniel |
| 2 | ERP desde VEN001 sin ensayo (prueba 2 paso 6) | Escobar |
| 3 | Filtro y correo sin prueba desde VEN001; regla 129 y 300 por confirmar | Pulido + Escobar |
| 4 | URL del webmail no documentada | Pulido |
| 5 | CMS: publicación y 403 sin ensayo de punta a punta | Daniel |
| 6 | Alerta CPU sin disparo; pico en vivo sin ensayo | Esteban |
| 7 | "Servidor sin Internet" sin evidencia registrada | Esteban / Escobar |
| 8 | Wazuh sin indexer/dashboard; sin fuentes VPN/proxy/nginx/AD | Esteban |
| 9 | Fuente NTP / PDC de AD001 no verificados | Esteban + Nicolás |
| 10 | NVR sin grabación continua | Esteban |
| 11 | GPO no aplicada a clientes; políticas adicionales sin evidencia | Nicolás |
| 12 | LDAPS falló; PKI sin distribución de raíz | Nicolás |
| 13 | SMB DKR002→DKR001 timeout | Escobar + Nicolás |
| 14 | Sin backup del ERP ni copia off-host; RTO no medido | Nicolás + Daniel |
| 15 | DKR001 sale sin proxy (excepción declarada) | Escobar |
| 16 | DHCP solo en red Docker aislada | Pulido |
| 17 | DNS003 no es la autoridad pública | Pulido |
| 18 | Sin IaC ni CI/CD evidenciados | Equipo |
| 19 | Sin alta disponibilidad | Equipo |
| 20 | PAM de VPN acepta usuarios locales | Escobar |
| 21 | Matriz de aprovisionamiento final y .pkt sin consolidar | Escobar / Pulido |
