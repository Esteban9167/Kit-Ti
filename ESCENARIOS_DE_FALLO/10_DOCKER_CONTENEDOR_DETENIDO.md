# 10 · Contenedor Docker detenido

[⬅ Plan B](../07_PLAN_B_SI_ALGO_FALLA.md) · [Docker](../PREGUNTAS_TRANSVERSALES/04_CONTENEDORES_DOCKER.md)

`[DKR001]`/`[DKR002]` 🟢:
```bash
sudo docker ps -a --format "table {{.Names}}\t{{.Status}}"
sudo docker logs --tail 30 NOMBRE_CONTENEDOR
sudo docker inspect --format '{{.Name}}: {{.HostConfig.RestartPolicy.Name}} {{.State.ExitCode}}' NOMBRE_CONTENEDOR
```
| Estado | Lectura |
|---|---|
| `Exited (0)` | Se detuvo limpiamente (pasó con Prometheus) |
| `Exited (≠0)` | Error: leer logs |
| `Restarting` | Bucle de fallo |
**Arrancar** (🟡, solo el dueño o con su aviso): `sudo docker start NOMBRE_CONTENEDOR`. Nunca `docker rm`, `docker compose down` ni `docker system prune` en vivo (🔴).
