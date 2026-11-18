# Prueba 10 · CMS / Intranet (grado 2) — 🟠 (ensayar completa)

[⬅ Índice pruebas](00_INDICE_PRUEBAS.md) · [Drupal](../DANIEL/06_CMS_DRUPAL.md)

**Rúbrica:** "La intranet solo es accesible desde la red interna. El administrador de contenido publica una noticia interna. La URL tiene certificado SSL."

**Responsable:** Daniel. **Estado:** Drupal instalado y regla allow/deny en LBR001 ✅ · candado por VPN, publicación y 403 externo ⏳ (no ejecutados de punta a punta al 17-nov).

| # | Dónde | Hacer | Ver |
|---|---|---|---|
| 1 | `[NAVEGADOR]` VPN + `hosts` | https://cms.langustino-lab.duckdns.org | **LANgustino Intranet** con candado |
| 2 | `[NAVEGADOR]` | Login admin Drupal (gestor: "Drupal CMS001 - admin") | Barra negra de administración |
| 3 | `[NAVEGADOR]` 🟡 | Content → + Add content → Article | Formulario Create Article |
| 4 | `[NAVEGADOR]` 🟡 | Title: *Noticia interna: jornada de seguridad en planta* · Body: una línea · Published ✔ · Save | "Article … has been created" |
| 5 | `[NAVEGADOR]` | Home | Noticia en la portada |
| 6 | `[CELULAR]` datos, sin VPN | Misma URL | **403 Forbidden** |

**Qué decir:** "La intranet es Drupal en el contenedor SRVD1DEVCMS001 en DKR002, con base en SQL001. Se publica por el mismo balanceador con el certificado de Let's Encrypt. nginx solo acepta la red interna 10.1.0.0/16, que incluye la VPN; a cualquier otra IP le responde 403, como se ve en el celular. Como administrador de contenido publico una noticia interna visible para los empleados conectados."

**Componentes:** navegador → VPN → LBR001 :443 (allow 10.x / deny all) → CMS001 :8083 en DKR002 (reglas NSG 200/210) → SQL001 :5432 (base `cms`).

| Fallo | Acción |
|---|---|
| No accede | VPN y línea `cms` en `hosts` |
| 502 | DKR002 apagada o contenedor detenido |
| Primera carga lenta | Normal (caché de Drupal): abrir minutos antes |
