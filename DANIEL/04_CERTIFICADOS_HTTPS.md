# 04 · Certificados HTTPS y balanceador LBR001

[⬅ Índice Daniel](00_INDICE_DANIEL.md) · [P07](../PRUEBAS_RECORRIDO/P07_CERTIFICADO_DIGITAL.md) · [Certificados (transversal)](../PREGUNTAS_TRANSVERSALES/07_CERTIFICADOS_Y_CIFRADO.md)

**Ficha:** `SRVD1DEVLBR001` · nginx 1.27 en DKR001 · TCP 80/443 · IP pública **172.212.220.169** (pip-lbr-dkr001) · certificado **comodín `*.langustino-lab.duckdns.org`**, Let's Encrypt, **válido hasta 15-feb-2027** · obtenido por **DNS-01** en DuckDNS · renovación: **cron diario**, renueva si faltan < 30 días.

**Política por nombre:** `lms` público · `erp` y `cms` **allow 10.1.0.0/16, deny all → 403**.

### PREGUNTA C1 · ¿Dónde termina TLS?
**🗣️ ORAL:** "En LBR001, el nginx de DKR001. De ahí al Odoo (8069), Moodle (8082) y Drupal (8083) el tráfico va por HTTP dentro de la red privada de Azure. Es una terminación en el borde; el siguiente paso sería re-cifrar hacia el backend con la PKI interna."

**📌** ✅.

### PREGUNTA C2 · ¿Por qué DNS-01 y no HTTP-01?
"DNS-01 permite certificados comodín y no exige exponer cada servicio a Internet para validar; demostramos control del dominio con un TXT temporal en DuckDNS."

### PREGUNTA C3 · ¿Cómo se ve el certificado? (🟢)
`[NAVEGADOR]` candado → certificado. Alternativa ◇ desde `[DKR001]`:
```bash
echo | openssl s_client -connect 127.0.0.1:443 -servername lms.langustino-lab.duckdns.org 2>/dev/null | openssl x509 -noout -subject -issuer -enddate
```
