# 05 · LMS — Moodle

[⬅ Índice Daniel](00_INDICE_DANIEL.md) · [P09](../PRUEBAS_RECORRIDO/P09_LMS.md)

**Ficha:** `SRVD1DEVLMS001` · Moodle 4.5 en DKR001 · TCP 8082 · **público** por LBR001 (https://lms.langustino-lab.duckdns.org) · BD `lms` en SQL001 (regla 158). Título: *Moodle LANgustino - Capacitacion*.

### PREGUNTA L1 · ¿Por qué el LMS es el único público?
"La rúbrica pide que el LMS sea accesible desde Internet para capacitación; ERP e intranet son internos y devuelven 403 fuera de la red interna."
### PREGUNTA L2 · ¿Cómo se protege si es público?
"Solo 80/443 en la IP pública, TLS con CA reconocida, la base no está expuesta (SQL001 solo acepta 5432 desde DKR001 por regla 158)."

**📌** ✅.
