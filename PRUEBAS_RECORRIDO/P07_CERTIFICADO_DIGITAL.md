# Prueba 7 · Certificado digital (grado 2) — ✅

[⬅ Índice pruebas](00_INDICE_PRUEBAS.md) · [Daniel certificados](../DANIEL/04_CERTIFICADOS_HTTPS.md)

**Rúbrica:** "Se accede al ERP por HTTPS." · **Responsable:** Daniel.

| # | Dónde | Hacer | Ver |
|---|---|---|---|
| 1 | `[NAVEGADOR]` con VPN + `hosts` | https://erp.langustino-lab.duckdns.org | Odoo con candado, sin advertencias |
| 2 | `[NAVEGADOR]` | Candado → La conexión es segura → El certificado es válido | Enviado a `*.langustino-lab.duckdns.org` · Emitido por **Let's Encrypt** · Válido hasta **15-feb-2027** |
| 3 (opcional) | `[CELULAR]` datos, sin VPN | Misma URL | **403 Forbidden** (ERP no expuesto) |

**Qué decir:** "El certificado lo termina el balanceador SRVD1DEVLBR001, un nginx en DKR001 con la única IP pública web, solo 80 y 443. Es un comodín de Let's Encrypt, CA reconocida, por eso no hay advertencias. Lo obtuvimos con el desafío DNS-01: un registro TXT temporal en DuckDNS prueba que el dominio es nuestro sin exponer servicios. Dura 90 días y un cron diario lo renueva cuando faltan menos de 30. nginx reenvía a Odoo en 10.1.100.70:8069 y solo acepta la red interna o la VPN; desde Internet, 403."

**Componentes:** navegador → VPN → LBR001 :443 (TLS termina aquí) → ERP001 :8069 (HTTP interno) → SQL001 :5432.

| Fallo | Acción |
|---|---|
| No se puede acceder | VPN y línea `erp` en `hosts` |
| 403 estando en VPN | El nombre resuelve a la IP pública: revisar `hosts`, `ipconfig /flushdns` |
| 502 | ERP001 apagada u Odoo no arrancó |
