# Prueba 9 · LMS (grado 2) — ✅

[⬅ Índice pruebas](00_INDICE_PRUEBAS.md) · [Moodle](../DANIEL/05_LMS_MOODLE.md)

**Rúbrica:** "El LMS es accesible desde internet y su URL tiene certificado SSL." · **Responsable:** Daniel. **Única prueba SIN VPN.**

| # | Dónde | Hacer | Ver |
|---|---|---|---|
| 1 | `[CELULAR]` datos, Wi-Fi off (o PC sin VPN) | https://lms.langustino-lab.duckdns.org | **Moodle LANgustino - Capacitacion** con candado |
| 2 | `[CELULAR]` | Candado → certificado | Let's Encrypt, `*.langustino-lab.duckdns.org` |
| 3 (opc.) | `[CELULAR]` | Login admin Moodle (gestor: "Moodle LMS001 - admin") | Panel |

Proyectar o pasarle el celular a la profesora.

**Qué decir:** "Abro el LMS desde Internet, sin VPN. El nombre apunta a la IP pública del balanceador, 172.212.220.169, que solo abre 80 y 443. LBR001 termina TLS con el mismo comodín de Let's Encrypt y reenvía a Moodle, el contenedor SRVD1DEVLMS001 en el mismo host. La base está en SQL001. Es el único servicio público porque la rúbrica lo pide; ERP e intranet solo desde la red interna."

**Componentes:** Internet → pip-lbr-dkr001 :443 → LBR001 → LMS001 :8082 → SQL001 :5432 (base `lms`, regla NSG 158).

| Fallo | Acción |
|---|---|
| No carga | DKR001 apagada |
| Redirige a 10.1.100.41 | Configuración de Moodle (`wwwroot`): avisar a Daniel |
