# 07 · Preguntas difíciles — Daniel

[⬅ Índice Daniel](00_INDICE_DANIEL.md)

| Nivel | Pregunta | Respuesta (≤20 s) |
|---|---|---|
| Original | ¿Qué pasa si el ERP no responde durante una llamada? | Timeout 3 s; el IVR avisa y cuelga. |
| Básica | ¿Qué CA emitió el certificado? | Let's Encrypt; comodín `*.langustino-lab.duckdns.org`, válido hasta 15-feb-2027. |
| Intermedia | ¿Cuánto dura y cómo se renueva? | 90 días; cron diario que renueva con < 30 días. |
| Intermedia | ¿Por qué el ERP da 403 desde Internet? | nginx permite solo 10.1.0.0/16. |
| Intermedia | ¿Por qué hay que editar `hosts`? | Para que `erp`/`cms` resuelvan a la IP interna 10.1.100.41 por la VPN (si no, van a la IP pública y reciben 403). Alternativa: registro en DNS001. |
| Avanzada | ¿Qué pasa si DKR001 se apaga? | Caen LBR001 y LMS: pruebas 7, 9 y 10 (y 2 en el acceso web). |
| Avanzada | ¿Por qué DKR001 sale a Internet sin proxy? | Tiene la IP pública del balanceador; excepción declarada en el diagrama. (Que facilite la renovación del certificado es análisis, no documentado.) |
| Crítica | ¿Hay respaldo del ERP? | No confirmado; pendiente con Nicolás. |
| Crítica | ¿El IVR depende del esquema de Odoo? | Sí, lee la base con `ivr_ro`; trade-off por simplicidad y latencia. |
