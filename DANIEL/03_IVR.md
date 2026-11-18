# 03 · IVR001 — Consulta de pedidos

[⬅ Índice Daniel](00_INDICE_DANIEL.md) · [P01](../PRUEBAS_RECORRIDO/P01_IVR.md)

**Ficha:** contenedor `SRVD1DEVIVR001` en DKR002 · Asterisk 18 + Python · SIP **5062** · RTP **UDP 10050-10099** · lee SQL001 con el usuario **`ivr_ro`** (solo lectura: ve número, estado y NIT; se le niega el total) · timeout de consulta **3 s**.

**Flujo:** MicroSIP (ext. 101) → VPN → PBX001:5060 → (500) → IVR001:5062 → pide NIT (900123456#) → pide pedido (1#) → consulta SQL001:5432 → "Su pedido está confirmado".

### PREGUNTA I1 · ¿Cómo sabe el IVR el estado del pedido?
**🗣️ ORAL:** "El IVR consulta directamente la base del ERP en SQL001 con un usuario de solo lectura que solo ve número, estado y NIT. El estado 'sale' de Odoo se traduce a 'su pedido está confirmado'."

**📌** ✅ probado con NIT correcto e incorrecto (NIT 123 → "no encontramos", sin caerse).

### PREGUNTA I2 · ¿Por qué consultar la base y no la API de Odoo?
Respuesta defendible: "Menos latencia y menos piezas en el flujo de voz, con privilegio mínimo en la base." Riesgo reconocido: "acopla el IVR al esquema de Odoo; una API XML-RPC sería más desacoplada." *(Justificación de diseño, no documentada por Daniel: presentarla como análisis.)*

### PREGUNTA I3 · ¿Si el ERP/BD no responde?
"Timeout de 3 s: el IVR dice que el sistema no está disponible y cuelga, sin dejar la llamada colgada."
