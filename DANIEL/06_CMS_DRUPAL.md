# 06 · CMS — Drupal (intranet)

[⬅ Índice Daniel](00_INDICE_DANIEL.md) · [P10](../PRUEBAS_RECORRIDO/P10_CMS_INTRANET.md)

**Ficha:** `SRVD1DEVCMS001` · Drupal 10.6 en DKR002 · TCP 8083 · publicado por LBR001 (https://cms.langustino-lab.duckdns.org) con **allow 10.1.0.0/16 / deny all** · BD `cms` en SQL001 · NSG r3 200 (8082/8083 desde VPN) y 210 (desde LBR001 10.1.100.41).

### PREGUNTA D1 · ¿Cómo garantizan que solo es interna?
**🗣️ ORAL:** "nginx en LBR001 solo acepta orígenes 10.1.0.0/16, que incluye la VPN porque hace NAT a 10.1.110.10; cualquier otra IP recibe 403. Además el contenedor no tiene IP pública."
⚠️ El diagrama escribe "403 fuera de 10.0.0.0/16"; el documento de Daniel dice **10.1.0.0/16**. Decir "la red interna 10.1.0.0/16" y no discutir el detalle salvo que pregunten.

**📌** 🟠 configurado; publicación de noticia y 403 externo sin ensayo de punta a punta.
