# Prueba 4 · Filtro de navegación LAN (grado 3) — 🟠

[⬅ Índice pruebas](00_INDICE_PRUEBAS.md) · [Pulido Squid](../OTROS_INTEGRANTES/PULIDO/01_PROXY_FILTRO_SQUID.md)

**Rúbrica:** "Desde la máquina virtual del vendedor conectado a la red privada, el intento de abrir una red social da un error que lo impide. El vendedor puede navegar a una página externa autorizada. El administrador del filtro revisa en los logs los intentos del vendedor."

**Responsables:** Santiago Pulido (Squid) + Santiago Escobar (VEN001, NSG).

**Estado:** Facebook 403 y Google 200 vía `10.1.100.41:3128` **desde DKR001** ✅ · desde VEN001 ⏳ · regla NSG **129** (TCP 3128 desde 10.1.110.10 hacia 10.1.100.41) **propuesta el 17-nov, sin confirmación de creación**.

> ⚠️ Endpoint: el consolidado del 17-nov y las pruebas de Pulido usan **SRVD1DEVPRX001 en 10.1.100.41:3128** (filtro con 16 dominios). La guía anterior de Escobar usaba PXY001 10.1.100.40. **Usar 10.1.100.41:3128** salvo que el equipo confirme otra cosa.

## Prerrequisitos (🔴/🟡, antes — Escobar)
- Regla 129 en `nsg-langustino-cu`.
- En VEN001 (PowerShell admin): proxy `10.1.100.41:3128`, `ProxyOverride` `*.langustino-lab.duckdns.org;10.*;<local>`, `ProxyEnable 1`.

## Pasos

| # | Dónde | Hacer | Ver |
|---|---|---|---|
| 1 | `[VEN001]` Edge (VPN + proxy) | https://www.facebook.com | Error de acceso del proxy / `ERR_TUNNEL_CONNECTION_FAILED` |
| 2 | `[VEN001]` Edge | https://www.wikipedia.org | Carga normal |
| 3 | `[WINDOWS PC]` / `[MAC]` | `ssh -J USUARIO@10.1.120.10 ssdeti@10.1.100.41` / `ssh dkr1` | Prompt SRVD1DEVDKR001 |
| 4 | `[DKR001]` 🟢 | `sudo docker exec SRVD1DEVPRX001 sh -c "tail -n 30 /var/log/squid/access.log" \| grep -E "DENIED\|TUNNEL"` | `TCP_DENIED/403 … www.facebook.com:443` y `TCP_TUNNEL/200 … wikipedia` desde **10.1.110.10** |

## Qué decir
"El navegador del vendedor sale por el proxy corporativo, Squid en el contenedor SRVD1DEVPRX001. Facebook está en la lista de 16 dominios bloqueados y el proxy lo niega; Wikipedia no y carga. Como administrador reviso el registro: el intento denegado y el permitido con hora y destino. La IP de origen es la del servidor de VPN porque la VPN hace NAT; para saber qué usuario fue, se cruza la hora con el log de la VPN. Como es HTTPS, el proxy decide por el nombre del sitio en el CONNECT, no inspecciona el contenido."

## Plan B (🟢, ya probado por Pulido) — desde `[DKR001]`
```bash
curl -s -o /dev/null -w "%{http_code}\n" -x http://10.1.100.41:3128 https://www.google.com      # 200
curl -v -x http://10.1.100.41:3128 https://www.facebook.com -o /dev/null 2>&1 | grep -E "HTTP/|CONNECT|403"   # 403
```
Decir que el origen aquí es DKR001, no el vendedor (vale menos: 2,5 probable).

## Si falla
| Síntoma | Acción |
|---|---|
| Facebook carga | Edge no usa el proxy → revisar configuración y reabrir Edge |
| Nada carga | Falta regla 129 o VPN caída |
| Log vacío | El navegador no pasó por el proxy |
