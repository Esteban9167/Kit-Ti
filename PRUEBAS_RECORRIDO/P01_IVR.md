# Prueba 1 · IVR (grado 5) — ✅ probada completa el 17-nov

[⬅ Índice pruebas](00_INDICE_PRUEBAS.md) · [Daniel IVR](../DANIEL/03_IVR.md)

**Rúbrica:** "Se demuestra algún caso de uso del IVR. Se consulta un pedido indicando el NIT, y ese pedido está guardado en el ERP."

**Responsable:** Daniel Sanabria · **Dependencias:** VPN001 + reglas NSG 220/230/240 (Escobar), ERP001, SQL001, DKR002.

## Preparar antes
- `[WINDOWS PC]` VPN conectada; MicroSIP **En línea**, transporte **TCP** (cuenta `LANgustino 101`, servidor SIP 10.1.100.102, usuario 101, dominio 10.1.100.102; clave "PBX001 - ext 101" del gestor). Codecs solo G.711 A-law/u-law; video deshabilitado.
- La ext. 101 admite **un solo dispositivo**: el que presenta se registra de último.
- `[NAVEGADOR]` https://erp.langustino-lab.duckdns.org con sesión de admin de Odoo.

## Pasos (90 s)

| # | Dónde | Hacer | Ver / escuchar |
|---|---|---|---|
| 1 | `[NAVEGADOR]` Odoo | Sales → Orders → Quotations → quitar filtro *My Quotations* (✕) → abrir **S00001** | Cliente *Cliente Prueba LANgustino*, estado **Sales Order** |
| 2 | `[NAVEGADOR]` | Clic en el cliente | **VAT 900123456** |
| 3 | `[MICROSIP]` | Marcar **500**, botón verde, subir volumen | "Bienvenido a LANgustino. Servicio de consulta de pedidos" |
| 4 | `[MICROSIP]` teclado | `9 0 0 1 2 3 4 5 6` y `#` | "Digite el número de su pedido…" |
| 5 | `[MICROSIP]` | `1` y `#` | "Su pedido está confirmado" → "Gracias por comunicarse…" (cuelga sola) |

## Qué decir
"El pedido S00001 está guardado en Odoo con el cliente de NIT 900123456. Llamo como cliente a la extensión 500. La llamada sale de mi softphone por la VPN hacia la PBX, un Asterisk en el contenedor SRVD1DEVPBX001. La PBX la pasa al IVR, otro Asterisk en SRVD1DEVIVR001, que pide NIT y pedido. El IVR consulta el estado directamente en la base del ERP en SRVD1DEVSQL001 con un usuario de solo lectura, `ivr_ro`, que solo ve número, estado y NIT. El estado de Odoo se traduce en el mensaje 'su pedido está confirmado'."

**Componentes:** MicroSIP → VPN001 (UDP 1194) → PBX001 SIP 5060 (DKR002) → IVR001 SIP 5062 → SQL001:5432 (`ivr_ro`) ← datos de ERP001.

## Si preguntan
- *¿Y si el ERP no responde?* "La consulta tiene límite de 3 segundos; si la base no responde, el IVR dice que el sistema no está disponible y cuelga."
- *¿NIT incorrecto?* Probado: con NIT 123 dice "no encontramos" sin caerse.
- *¿Por qué solo lectura?* Mínimo privilegio: a `ivr_ro` se le niega incluso el total del pedido.

## Si falla
| Síntoma | Acción |
|---|---|
| MicroSIP no dice En línea | Reconectar VPN; transporte TCP |
| "Solicitud expiró" | Transporte quedó en UDP → TCP |
| Bienvenida sin estado | Repetir marcando despacio, terminar cada dato con `#` |
| Sin audio | Volumen; codecs G.711 |
