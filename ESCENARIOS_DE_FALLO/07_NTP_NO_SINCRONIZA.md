# 07 · NTP no sincroniza

[⬅ Plan B](../07_PLAN_B_SI_ALGO_FALLA.md) · [NTP](../ESTEBAN/05_NTP.md)

`[DKR002]`/`[BASTION]`/`[SIEM001]` 🟢: `chronyc sources -v` · `chronyc tracking` · `sudo chronyd -Q -t 15 'server 10.1.100.41 iburst'`
| Indicador | Significado | Acción |
|---|---|---|
| `^*` en 10.1.100.41 | Seleccionada ✅ | — |
| `^-` / `^+` | Candidata, no seleccionada | Explicar; PHC0 de respaldo |
| `^?` / Reach 0 | Sin respuesta | DKR001 apagado, contenedor NTP001 detenido o regla 125-127 |
| `^x` | Falseticker | Mostrar captura NTP-01..03 |
| AD001 | No verificado | Decir: "solo probamos que responde" |
**NO hacer:** `chronyc makestep` ni reiniciar chrony en vivo.
