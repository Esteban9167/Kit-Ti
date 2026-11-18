# 01 · Servidor apagado

[⬅ Plan B](../07_PLAN_B_SI_ALGO_FALLA.md)

**Síntoma:** SSH/HTTP con timeout; Prometheus `up=0`; 502 en web.
| Paso | Dónde | Acción | Riesgo |
|---|---|---|---|
| 1 | `[AZURE PORTAL]` | rg-langustino-dev → VM → Estado (*Running* / *Deallocated*) | 🟢 |
| 2 | `[CLOUD SHELL]` | `az vm list -g rg-langustino-dev -d --query "[].{VM:name,Estado:powerState}" -o table` | 🟢 |
| 3 | `[AZURE PORTAL]` | **Iniciar** la VM (tarda 1–3 min; Drupal/Odoo tardan más en responder) | 🟡 (aceptable: es encender lo que debió estar encendido) |
**Qué decir:** "La VM estaba detenida por el auto-apagado de costo; la enciendo y mientras arranca explico el flujo con el diagrama."

**Recordatorio:** encender las 10 VM ~15 min antes; VEN001 tenía auto-apagado a las 6 p. m.
