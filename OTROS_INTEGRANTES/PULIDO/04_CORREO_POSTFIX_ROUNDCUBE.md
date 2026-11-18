# 04 · Correo — Postfix + Dovecot + Roundcube (REQ-COM-03)

[⬅ Índice Pulido](00_INDICE_PULIDO.md) · [P05](../../PRUEBAS_RECORRIDO/P05_CORREO.md)

**Ficha (DKR002):** `SRVD1DEVMTA001` Postfix (SMTP) + Dovecot (IMAP/buzones) · `SRVD1DEVWBM001` Roundcube · `SRVD1DEVHTTPSWBM001` proxy TLS del webmail · buzones operario1, vendedor1, soporte1 (locales; LDAP opcional) · relay externo bloqueado (554 observado antes).

| Requisito | Estado |
|---|---|
| Relay SMTP | Configurado; comprobar política final |
| Buzones y webmail | Funcionamiento interno reportado |
| Envío interno | Verificar en vivo |
| Envío externo bloqueado | 554 observado; no confirmado en política |
| Rechazo de remitente externo | Verificar |
| **URL/puerto del webmail** | **No registrado** — descubrir con `docker ps` / `ss -lntp` |

### PREGUNTA CO1 · ¿Cómo impide el servidor la fuga a correo personal?
**🗣️ ORAL:** "Postfix solo entrega a dominios locales; un destino externo recibe un error 5xx, se observó 554, y queda en el log."

**⚠️** No atribuir el 554 automáticamente al bloqueo de puerto 25 de Azure sin leer el log.

**⌨️ (🟢)** `[DKR002]`: `sudo docker logs SRVD1DEVMTA001 --since 10m | grep -iE "reject|554"` · `sudo docker exec SRVD1DEVMTA001 postqueue -p`
