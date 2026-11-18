# 06 · BKP001 — Backups con Restic

[⬅ Índice Nicolás](00_INDICE_NICOLAS.md) · [Backup (transversal)](../PREGUNTAS_TRANSVERSALES/08_BACKUP_Y_RECUPERACION.md)

**Ficha:** `restic/rest-server:0.14.0` en DKR001 **127.0.0.1:8000** (no expuesto) · volumen `langustino-bkp-data` · trabajos con `restic/restic:0.18.1` desde scripts en `~/langustino/bkp001/`.

| Origen | Frecuencia (cron) | Evidencia |
|---|---|---|
| FIL001 `/datos` | `*/15 * * * *` | Snapshot 804d82ba; LEEME.txt 34 B |
| MSA001 `/postgresql/msa.dump` (pg_dump desde SQL001) | `5,20,35,50 * * * *` | Snapshot 804ba6f1; dump 155.160 KiB; **restauración 58 tablas** |
| Integridad | 17-nov | `check --read-data`: 20/20 snapshots, 20/20 paquetes, **no errors were found** |
| ERP | — | ⏳ sin job confirmado |
| Copia fuera de DKR001 | — | ⏳ no existe |

### PREGUNTA K1 · ¿Cómo saben que un backup puede restaurarse?
**🗣️ ORAL:** "No basta con guardar snapshots. Restauramos el snapshot 804ba6f1 del dump de la mesa de ayuda en un PostgreSQL 14 temporal sin red, y verificamos 58 tablas restauradas, sin tocar producción."

**🖥️** `[DKR001]` · **⌨️ (🟢)**:
```bash
crontab -l | grep -E "backup-(fil|msa)"
tail -n 12 ~/langustino/bkp001/backup-fil.log
tail -n 12 ~/langustino/bkp001/backup-msa.log
ls -lh ~/langustino/bkp001/
```

**✅** dos líneas de cron escalonadas · `snapshot … saved` recientes.

**Resultado observado de la restauración (18-nov):** `restoring snapshot 804ba6f1…` · `Restored 2 files/dirs (155.160 KiB)` · `TABLAS RESTAURADAS: 58` · `RESTAURACION COMPLETADA`.

**📌** ✅.

### Repetir la restauración (🟡 crea contenedor temporal `--network none`, se borra solo; solo si sobra tiempo)
```bash
if docker ps -a --format "{{.Names}}" | grep -qx bkp-msa-restore-test; then
  echo "DETENIDO: contenedor de prueba ya existe"
else
  sed "s/54c12c1d/804ba6f1/" ~/langustino/bkp001/verificar-restauracion.sh > /tmp/verificar-restauracion-actual.sh
  bash /tmp/verificar-restauracion-actual.sh
fi
```
Si el repositorio se depuró, 804ba6f1 puede no existir: sustituir por un snapshot actual.
`check --read-data` (🟢 pero lento): solo con tiempo. **Nunca** `cat restic.env` ni `pg-msa.env`.

### PREGUNTA K2 · ¿Cumplen RPO de 15 min para el ERP?
"No está demostrado. Los 15 min aplican a FIL001 y a la base msa. El respaldo del ERP, la recuperación del pedido S00001 y el RTO medido están pendientes."

### PREGUNTA K3 · ¿Qué pasa si se pierde DKR001?
"Se pierde también el repositorio de respaldos, porque está en el mismo host. Falta la copia off-host (por ejemplo a Azure Blob Storage o a otra VM)."
