# Prueba 5 · Correo (grado 3) — 🟠 ALTO RIESGO

[⬅ Índice pruebas](00_INDICE_PRUEBAS.md) · [Pulido correo](../OTROS_INTEGRANTES/PULIDO/04_CORREO_POSTFIX_ROUNDCUBE.md)

**Rúbrica:** "Hay al menos dos buzones con el dominio de LANgustino. El vendedor envía un correo a otro empleado con éxito. Al intentar enviarse información de la empresa a su correo personal, la configuración del servidor se lo impide."

**Responsable:** Santiago Pulido · **Dependencias:** Escobar (regla NSG **300** en `nsg-langustino-r3` desde 10.1.110.10 hacia 10.1.100.102 al puerto del webmail — ⏳), VEN001.

## Estado real
- Buzones: **operario1, vendedor1, soporte1** (cuentas locales del correo; LDAP opcional).
- Envío interno: **reportado** por Pulido; falta captura en vivo.
- Rechazo externo: se **observó SMTP 554** en una prueba anterior; política final por confirmar.
- **URL/puerto del webmail: NO documentados.** No suponer que `mail.langustino-lab.duckdns.org` abre Roundcube (solo apunta a DKR002).

## Descubrir la URL (🟢, Pulido antes de la sesión)
```bash
# [DKR002]
sudo docker ps --format "table {{.Names}}\t{{.Image}}\t{{.Ports}}" | grep -iE "WBM|MTA"
sudo ss -lntp | grep -E ":(80|443|8080|8081|8084|8888)\b"
```

## Pasos

| # | Dónde | Hacer | Ver |
|---|---|---|---|
| 1 | `[VEN001]` Edge (VPN) | Abrir webmail (URL confirmada) → login **vendedor1** | Bandeja |
| 2 | `[VEN001]` | Redactar a **operario1@[dominio LANgustino]** → Enviar | Enviado sin error |
| 3 | `[VEN001]` | Cerrar sesión → entrar como operario1 | Correo en Entrada |
| 4 | `[VEN001]` | Como vendedor1, enviar con adjunto a cuenta personal (Gmail) | Error de entrega / rebote **554** |
| 5 | `[DKR002]` 🟢 | `sudo docker logs SRVD1DEVMTA001 --since 10m \| grep -iE "reject\|554"` | Línea del rechazo con el destinatario externo |

## Qué decir
"El correo corre en DKR002: Postfix envía y recibe, Dovecot guarda los buzones y Roundcube es el webmail, publicado con TLS por SRVD1DEVHTTPSWBM001. El vendedor le escribe a un operario y llega. Si intenta enviarse información a su correo personal, el servidor lo rechaza porque solo entrega dentro del dominio, y el log de Postfix lo muestra."

**Componentes:** VEN001 → VPN → HTTPSWBM001 (TLS) → WBM001 Roundcube → MTA001 Postfix (SMTP) / Dovecot (IMAP) en DKR002.

## Si falla
| Síntoma | Acción |
|---|---|
| Webmail no abre | Falta regla 300 o URL incorrecta |
| Interno no llega | `sudo docker exec SRVD1DEVMTA001 postqueue -p` 🟢 |
| El externo **sale** | La restricción no está aplicada → **no decir que está bloqueado**; avisar a Pulido |
| 554 | Identificar quién lo emite en el log; no atribuirlo automáticamente al bloqueo de puerto 25 de Azure |
