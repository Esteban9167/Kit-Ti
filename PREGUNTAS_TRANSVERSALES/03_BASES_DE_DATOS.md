# 03 · Bases de datos

[⬅ README](../README.md) · [ERP/SQL](../DANIEL/01_ERP_ODOO_INTEGRACION.md)

**SQL001** 10.1.100.71 · PostgreSQL 14 · bases `langustino` (Odoo), `lms`, `cms`, `msa` · un usuario y una regla 5432 por aplicación.

| Cliente | Base | Regla NSG | Integrante |
|---|---|---|---|
| ERP001 Odoo | langustino | nc 150 | Daniel |
| IVR001 (`ivr_ro`, solo lectura) | langustino | nc 155 (DKR002) | Daniel |
| CMS001 Drupal | cms | nc 155 | Daniel |
| MSA001 Redmine | msa | nc 155 | Nicolás |
| LMS001 Moodle | lms | nc 158 (DKR001) | Daniel |
| BKP001 pg_dump | msa | nc 158 (DKR001) | Nicolás |

### PREGUNTA BD1 · ¿Qué ocurre si falla PostgreSQL?
**🗣️ ORAL:** "SQL001 es punto único de falla: caen ERP, IVR, LMS, CMS y mesa de ayuda, y con ellos las pruebas 1, 2, 3, 7, 9 y 10. En DEV lo aceptamos; en producción usaríamos réplica o un servicio administrado. Para msa tenemos respaldo cada 15 min con restauración probada; para el ERP falta."

**🔍 Diagnóstico 🟢:** `ssh -J bastion ssdeti@10.1.100.71` → `systemctl status postgresql --no-pager` ◇ · `sudo ss -lntp | grep 5432` ◇.

### PREGUNTA BD2 · ¿Por qué un usuario de solo lectura para el IVR?
Mínimo privilegio: solo número, estado y NIT; se le niega el total (verificado).
