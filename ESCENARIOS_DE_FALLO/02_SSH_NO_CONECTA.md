# 02 · SSH no conecta

[⬅ Plan B](../07_PLAN_B_SI_ALGO_FALLA.md) · [Acceso](../02_RUTA_DE_ACCESO_A_SERVIDORES.md)

| Mensaje | Causa probable | Acción 🟢 |
|---|---|---|
| Se queda esperando / `Operation timed out` | VPN caída o BAS001 apagada | Reconectar VPN; portal |
| `Permission denied (publickey)` | Llave no autorizada / usuario equivocado | Verificar usuario (`esteban`, `daniel`…); usar el equipo de quien sí tiene llave |
| `Could not resolve hostname dkr1` | Alias no existe en ese equipo | Usar forma larga `-J USUARIO@10.1.120.10 ssdeti@IP` |
| `Broken pipe` / `Connection reset by peer` | Intermitencia observada | Reabrir; no reiniciar sshd |
| `bind: Address already in use` (túnel) | Túnel previo abierto | Reutilizar o cambiar puerto local |
| `REMOTE HOST IDENTIFICATION HAS CHANGED` | VM recreada | **No** borrar known_hosts en vivo sin pensar; avisar |
**NO hacer:** editar `sshd_config`, NSG o reiniciar el bastión en vivo.
