# 08 · NVR sin video

[⬅ Plan B](../07_PLAN_B_SI_ALGO_FALLA.md) · [NVR](../ESTEBAN/07_NVR_MEDIAMTX_FFMPEG.md)

`[DKR002]` 🟢:
```bash
sudo docker ps -a --filter "name=SRVD1DEVNVR001" --filter "name=SRVD1DEVCAM001"
sudo docker logs --tail 25 SRVD1DEVNVR001
sudo docker logs --tail 25 SRVD1DEVCAM001
ls -lh /opt/langustino-nvr/recordings/
```
| Resultado | Causa | Acción |
|---|---|---|
| CAM001 `Exited` | FFmpeg detenido → no publica | Mostrar MP4 y captura NVR-01; arrancar sería 🟡 (`cd /opt/langustino-nvr && sudo docker compose up -d` ◇) |
| NVR001 `Exited` | MediaMTX caído | Ídem |
| Logs sin "online" | Cámara aún conectando | Esperar 10 s y repetir |
| Sin MP4 | — | Reproducir la copia en `~/Downloads` del Mac |
