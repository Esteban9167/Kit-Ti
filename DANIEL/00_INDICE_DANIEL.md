# Daniel Sanabria — Índice

[⬅ README](../README.md)

**Componentes (según guía técnica y documento de pruebas 1, 7, 9, 10):** ERP Odoo 17 (ERP001) · PostgreSQL 14 (SQL001) · balanceador nginx LBR001 + certificado Let's Encrypt · PBX001 y IVR001 (Asterisk) · LMS Moodle · CMS Drupal. Pruebas: **1, 7, 9, 10** (y 3 con Nicolás). Suman 11 de 30 puntos.

| Archivo | Estado |
|---|---|
| [01_ERP_ODOO_INTEGRACION.md](01_ERP_ODOO_INTEGRACION.md) | ✅ ERP + SQL; ⏳ backup ERP |
| [02_PBX_ASTERISK.md](02_PBX_ASTERISK.md) | ✅ |
| [03_IVR.md](03_IVR.md) | ✅ prueba 1 completa |
| [04_CERTIFICADOS_HTTPS.md](04_CERTIFICADOS_HTTPS.md) | ✅ prueba 7 |
| [05_LMS_MOODLE.md](05_LMS_MOODLE.md) | ✅ prueba 9 |
| [06_CMS_DRUPAL.md](06_CMS_DRUPAL.md) | 🟠 prueba 10 sin ensayo completo |
| [07_PREGUNTAS_DIFICILES.md](07_PREGUNTAS_DIFICILES.md) | — |
| [08_GUION_DEMOSTRACION.md](08_GUION_DEMOSTRACION.md) | — |

**Servidores necesarios:** VPN001, BAS001, ERP001, SQL001, DKR001, DKR002. **Si ERP001 o SQL001 fallan, caen las pruebas 1 y 7 completas** (y 2, 3).
