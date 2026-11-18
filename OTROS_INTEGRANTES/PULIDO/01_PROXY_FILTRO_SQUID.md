# 01 · Filtro de navegación — Squid (SRVD1DEVPRX001)

[⬅ Índice Pulido](00_INDICE_PULIDO.md) · [P04](../../PRUEBAS_RECORRIDO/P04_FILTRO_NAVEGACION.md) · [P08](../../PRUEBAS_RECORRIDO/P08_SOFTWARE_AUTORIZADO.md)

**Ficha:** contenedor `SRVD1DEVPRX001` en DKR001 · **10.1.100.41:3128/TCP** · lista de **16 dominios bloqueados** (redes sociales, streaming, adultos, acceso remoto: anydesk.com, teamviewer.com, rustdesk.com) · **denegar por defecto**: solo 10.1.0.0/16 (y red de laboratorio) usa el proxy · HTTPS por **CONNECT sin inspección** (decide por nombre).

**No confundir** con PXY001 (VM 10.1.100.40, proxy de salida).

### PREGUNTA PX1 · Demuestre el bloqueo
**🗣️ ORAL:** "Squid aplica una lista de dominios: Facebook y AnyDesk devuelven 403 y Google 200. No inspecciona el contenido HTTPS; decide por el destino del CONNECT."

**🖥️** `[DKR001]` · **⌨️ (🟢):**
```bash
docker ps --filter name=SRVD1DEVPRX001
curl -v -x http://10.1.100.41:3128 https://www.facebook.com -o /dev/null 2>&1 | grep -E "HTTP/|CONNECT|403"
curl -s -o /dev/null -w "%{http_code}\n" -x http://10.1.100.41:3128 https://www.google.com
sudo docker exec SRVD1DEVPRX001 sh -c "tail -n 30 /var/log/squid/access.log"
```

**✅** `HTTP/1.1 403 Forbidden` · `200` · líneas `TCP_DENIED/403` y `TCP_TUNNEL/200`.

**📌** ✅ desde DKR001 · ⏳ desde VEN001 + log del vendedor.

### PREGUNTA PX2 · ¿Por qué en el log aparece 10.1.110.10?
"La VPN hace NAT; se identifica al usuario cruzando la hora con el log de la VPN."

### PREGUNTA PX3 · ¿Se puede evadir?
"Si el navegador no usa el proxy, sí: hoy se configura manualmente en VEN001. Lo ideal es forzarlo por GPO y bloquear la salida directa en red. Es una limitación declarada."
