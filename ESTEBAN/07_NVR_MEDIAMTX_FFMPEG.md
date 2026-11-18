# 07 · NVR — MediaMTX + FFmpeg

[⬅ Índice Esteban](00_INDICE_ESTEBAN.md) · [NVR sin video](../ESCENARIOS_DE_FALLO/08_NVR_SIN_VIDEO.md)

**Ficha:** DKR002 10.1.100.102 · carpeta **`/opt/langustino-nvr`** · Docker Compose.
| Contenedor | Imagen | Rol |
|---|---|---|
| SRVD1DEVNVR001 | `bluenviron/mediamtx:1` (MediaMTX v1.21.4) | Servidor RTSP / NVR |
| SRVD1DEVCAM001 | `linuxserver/ffmpeg:latest` | Cámara simulada (`testsrc2`, H.264) |

Flujo: CAM001 publica **rtsp://127.0.0.1:8554/camara1** (RTSP sobre TCP, **solo loopback**) → NVR001 lo recibe. H.264, 640×360. Se recuperó tras reinicio del host.
Grabación de prueba: `/opt/langustino-nvr/recordings/camara1_prueba.mp4` (~1,5 MB) + `evidencia_camara1.jpg` (~20 KB).

---

### PREGUNTA V1 · ¿Cómo demuestran que el NVR recibe video?
**🗣️ ORAL:** "Simulamos una cámara con FFmpeg que publica por RTSP; MediaMTX registra que el flujo está disponible y en línea, con una pista H.264."

**👤** Esteban · **🖥️** `[DKR002]` · **🔑** `ssh dkr2` · **📁** `cd /opt/langustino-nvr`

**⌨️ (🟢):**
```bash
sudo docker ps --filter "name=SRVD1DEVNVR001" --filter "name=SRVD1DEVCAM001"
sudo docker logs --tail 25 SRVD1DEVNVR001
```

**✅** ambos `Up` · `stream is available and online, 1 track (H264)` · `is publishing to path 'camara1'`.

**📌** ✅ 18-nov.

### PREGUNTA V2 · ¿Cómo prueban que la grabación es válida?
**🗣️ ORAL:** "Extrajimos un MP4 y lo validamos con ffprobe: códec H.264, 640×360, 9,4 segundos. Lo copiamos al Mac por SCP y se reproduce: un patrón sintético de colores y figuras."

**⌨️ (🟢, contenedor temporal `--rm`, volumen solo lectura):**
```bash
# [DKR002]
ls -lh /opt/langustino-nvr/recordings/
sudo docker run --rm --entrypoint ffprobe -v /opt/langustino-nvr/recordings:/recordings:ro linuxserver/ffmpeg:latest -v error -show_entries format=duration:stream=codec_name,width,height -of default=noprint_wrappers=1 /recordings/camara1_prueba.mp4
```
```bash
# [MAC]
scp dkr2:/opt/langustino-nvr/recordings/camara1_prueba.mp4 ~/Downloads/
open ~/Downloads/camara1_prueba.mp4
```

**✅** `codec_name=h264` · `width=640` · `height=360` · `duration=9.400000`.

### PREGUNTA V3 · ¿Graba automáticamente?
**🗣️ ORAL:** "No. Verificamos RTSP y una grabación de prueba; no configuramos grabación continua ni retención."

**📌** ❌ grabación continua.

### PREGUNTA V4 · ¿Cómo se comprueba el tráfico RTSP?
"Por los logs de MediaMTX (publicación y lectura en `camara1`). Como RTSP está en loopback 127.0.0.1:8554, no sale de DKR002; no hay regla NSG para RTSP, a propósito." Opcional ◇ 🟢: `sudo ss -ltnp | grep 8554`.

### PREGUNTA V5 · ¿Por qué loopback y no la red?
"Seguridad: la cámara y el NVR están en el mismo host; no hace falta exponer RTSP. Con cámaras reales en la VLAN 50 (reservas DHCP de ejemplo de Pulido) se abriría 8554 solo desde esa red."

> Puerto antiguo **8085** en la guía técnica = plan, **obsoleto**.
