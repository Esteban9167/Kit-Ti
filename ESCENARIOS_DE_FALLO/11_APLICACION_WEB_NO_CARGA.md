# 11 · Aplicación web no carga

[⬅ Plan B](../07_PLAN_B_SI_ALGO_FALLA.md)

| Error | Causa | Acción |
|---|---|---|
| "No se puede acceder al sitio" | VPN caída / falta `hosts` | Reconectar; `hosts` + `ipconfig /flushdns` |
| **403** estando en VPN | Nombre resolvió a la IP pública | Revisar `hosts` → 10.1.100.41 |
| **403** sin VPN (ERP/CMS) | **Correcto**: no expuestos | Usarlo como evidencia |
| **502 Bad Gateway** | Backend caído (ERP001 / DKR002 / contenedor) | Portal; `docker ps` |
| Moodle redirige a 10.1.100.41 | `wwwroot` de Moodle | Avisar a Daniel |
| Drupal lento | Caché tras arranque | Abrir minutos antes |
| Redmine no abre (http://10.1.100.102:8081) | VPN / DKR002 / contenedor | `docker compose ps` en `~/langustino/msa001` |
| Webmail no abre | URL no confirmada / regla 300 | Pulido |
`[DKR001]` 🟢 ◇: `sudo docker logs --tail 30 SRVD1DEVLBR001`
