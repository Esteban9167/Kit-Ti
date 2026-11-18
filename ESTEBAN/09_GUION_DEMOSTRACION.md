# 09 · Guion de demostración — Esteban (Escena 9 / Prueba 6)

[⬅ Índice Esteban](00_INDICE_ESTEBAN.md) · [P06](../PRUEBAS_RECORRIDO/P06_ADMINISTRACION_TI.md)

## Preparación (T-15 min) en el Mac

| Pestaña Terminal | Comando `[MAC]` | Queda en |
|---|---|---|
| 1 | `ssh bastion` | `[BASTION]` |
| 2 | `ssh dkr1` | `[DKR001]` |
| 3 | `ssh dkr2` → `cd /opt/langustino-nvr` | `[DKR002]` |
| 4 | `ssh siem` | `[SIEM001]` |
| 5 | `ssh -N -L 3000:127.0.0.1:3000 dkr1` | túnel Grafana |
| 6 | `ssh -o ExitOnForwardFailure=yes -N -L 13389:10.1.100.100:3389 bastion` | túnel RDP |

Navegador: http://localhost:3000 con **LANgustino - Monitoreo NOC** y **Windows Exporter Dashboard 2025** en dos pestañas. `~/Downloads/camara1_prueba.mp4` listo.
Comprobación previa en pestaña 2 (🟢): `sudo docker ps --format "table {{.Names}}\t{{.Status}}" | grep NOC001` → ambos `Up`.

## Secuencia (≈ 6 min completo; 90 s si es la prueba 6)

| # | Pestaña / pantalla | Acción | Frase |
|---|---|---|---|
| 1 | 1 `[BASTION]` | `sudo sshd -T \| grep -E "^(passwordauthentication\|permitrootlogin\|allowusers) "` | "La VPN da acceso privado; el bastión aplica control por llave y canaliza SSH/RDP." |
| 2 | Navegador Grafana NOC | Cambiar Job: bastion → dkr001 → dkr002 → siem001 | "Cuatro Node Exporter aportan CPU, RAM, red y disco." |
| 3 | Grafana Windows + pestaña 2 | `curl -sG --data-urlencode 'query=up' http://127.0.0.1:9090/api/v1/query \| python3 -m json.tool` | "AD001 con Windows Exporter 0.32.1; seis objetivos UP." |
| 4 | 3 `[DKR002]` | `chronyc tracking` | "La hora se sincroniza permanentemente contra 10.1.100.41, estado Normal." |
| 5 | 4 `[SIEM001]` | `sudo /var/ossec/bin/agent_control -l` | "Wazuh recibe eventos reales de cuatro hosts; 96 alertas en 30 minutos en el último corte." |
| 6 | 3 `[DKR002]` + reproductor | `sudo docker logs --tail 25 SRVD1DEVNVR001` y reproducir MP4 | "Cámara simulada H.264 por RTSP y grabación de 9,4 s reproducible." |
| 7 | — | Cierre | "Pendientes: fuente NTP de Windows/PDC, panel SIEM completo y más fuentes, y una prueba de disparo de alerta." |

## Si me toca la prueba 6 completa
Seguir [P06](../PRUEBAS_RECORRIDO/P06_ADMINISTRACION_TI.md) (incluye RDP, "sin Internet" y carga de CPU).

## Cierre limpio
`Ctrl+C` en pestañas 5 y 6 · `exit` en 1–4 · VPN se queda conectada hasta el final de la sesión del grupo.
