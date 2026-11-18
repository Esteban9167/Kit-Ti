# 08 · Backup y recuperación

[⬅ README](../README.md) · [Restic](../NICOLAS/06_BACKUPS_RESTIC.md)

| Qué | Respaldo | Restauración | RPO |
|---|---|---|---|
| FIL001 (Samba) | ✅ cada 15 min | ⏳ no evidenciada | 15 min |
| Base msa (Redmine) | ✅ cada 15 min (pg_dump) | ✅ 58 tablas (18-nov) | 15 min |
| Base ERP | ⏳ | ⏳ (recuperar S00001) | no demostrado |
| LMS/CMS | ⏳ no evidenciado | — | — |
| Configuración de contenedores | Compose en disco (no evidenciado versionado) | — | — |
| Repositorio | **en DKR001** | — | sin copia off-host |

### PREGUNTA BK1 · ¿Cómo sabemos que un backup puede restaurarse?
Ver [NICOLAS/06 K1](../NICOLAS/06_BACKUPS_RESTIC.md): restauración aislada (`--network none`) de 804ba6f1 → 58 tablas + `restic check --read-data` 20/20 sin errores.

### PREGUNTA BK2 · ¿RTO y RPO?
"RPO de 15 min demostrado solo para archivos y la base de la mesa de ayuda. RTO no medido. Para el ERP, pendiente."

### PREGUNTA BK3 · Backup ≠ alta disponibilidad
"El respaldo recupera datos después de una pérdida; no evita la interrupción. No tenemos alta disponibilidad."
