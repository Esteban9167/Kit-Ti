# 04 · FIL001 — Servidor de archivos Samba

[⬅ Índice Nicolás](00_INDICE_NICOLAS.md) · [P08](../PRUEBAS_RECORRIDO/P08_SOFTWARE_AUTORIZADO.md)

**Ficha:** contenedor `SRVD1DEVFIL001` · Samba en DKR001 **10.1.100.41:445/tcp** (solo IP privada) · recurso `//10.1.100.41/software` · carpeta host `~/langustino/fil001/software` montada **solo lectura** · cuentas Samba **locales** (no AD). Contenido al corte: `LEEME.txt` (34 B).

### PREGUNTA F1 · Demuestre el control de acceso
**🗣️ ORAL:** "El recurso de software autorizado es de solo lectura. Soporte lo puede listar; un operario recibe acceso denegado; y la escritura también se rechaza."

**🖥️** `[DKR001]` · **📁** `cd ~/langustino/fil001`

**⌨️ (🟢, clave interactiva, nunca en el comando):**
```bash
docker compose -f compose.yml config --quiet
docker ps --filter name=SRVD1DEVFIL001
find ~/langustino/fil001/software -maxdepth 2 -type f -printf "%P (%s bytes)\n" | head -30
smbclient //10.1.100.41/software -U soporte1 -c "ls"
smbclient //10.1.100.41/software -U operario1 -c "ls"
```

**✅** contenedor Up · `LEEME.txt (34 bytes)` · soporte1 lista · operario1 `NT_STATUS_ACCESS_DENIED`. (Escritura de soporte1 también `ACCESS_DENIED` en prueba anterior — no repetir.)

**⚠️** No mostrar `samba.env`.

**📌** ✅ local · ⏳ desde otra subred: **DKR002 → DKR001:445 dio timeout** (revisión NSG pendiente; no abrir 445 a Internet) · ⏳ integración con AD.

### PREGUNTA F2 · ¿Las cuentas son del dominio?
"No. Son cuentas locales de Samba con los mismos nombres; la integración con AD no está completada."
