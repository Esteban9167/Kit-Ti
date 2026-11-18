# 01 · Bastión SRVD1DEVBAS001 (REQ-ADM-03)

[⬅ Índice Esteban](00_INDICE_ESTEBAN.md) · [Acceso](../02_RUTA_DE_ACCESO_A_SERVIDORES.md)

**Ficha:** 10.1.120.10 · North Central US · snet-gestion 10.1.120.0/27 · B2ats_v2 2 vCPU / 1 GiB / 32 GB · Ubuntu 22.04 x64 · sin IP pública · node_exporter :9100 · agente Wazuh 001 · cliente NTP.

**Arquitectura:** Mac → VPN → BAS001 → Linux por SSH/ProxyJump · AD001:3389 por túnel SSH.

**NSG relevantes:** 160 (TCP 22 desde VPN 10.1.110.10 → 10.1.120.10) · 170 (TCP 22 desde 10.1.120.10 → 10.1.0.0/16) · 175 r3 (TCP 3389 10.1.120.10 → AD001) · 165 nc (TCP 9100 desde Prometheus).

---

### PREGUNTA B1 · ¿Cómo demuestran que el bastión está endurecido?

**🗣️ RESPUESTA ORAL (≤20 s):** "El bastión solo acepta llave pública, tiene contraseñas y root deshabilitados, y una lista blanca de cinco usuarios. Permite túneles para administrar servidores privados, pero `GatewayPorts no` impide que esos túneles se publiquen hacia otros."

**👤 RESPONSABLE:** Esteban Sequeda.

**🖥️ EQUIPO:** `[BASTION]` 10.1.120.10.

**🔑 ENTRAR:** `[MAC]` `ssh bastion` · `[WINDOWS PC]` `ssh USUARIO@10.1.120.10`.

**⌨️ COMANDO (🟢):**
```bash
sudo sshd -t && echo "Configuración SSH válida"
sudo sshd -T | grep -E "^(pubkeyauthentication|passwordauthentication|permitrootlogin|allowusers|allowtcpforwarding|gatewayports) "
```

**✅ RESULTADO:** `Configuración SSH válida` · `permitrootlogin no` · `pubkeyauthentication yes` · `passwordauthentication no` · `allowtcpforwarding yes` · `gatewayports no` · `allowusers daniel` … `esteban` `spulido` `escobar` `nicolas` (sshd -T muestra una línea por usuario).

**➕ EVIDENCIA:** Captura BAS-01.

**⚠️ SI FALLA:** No editar `sshd_config` en vivo. Si `sudo` pide clave y no la tienes, mostrar captura.

**📌 ESTADO:** ✅ Verificado (guía Esteban 18-nov).

**📚 FUENTE:** Pruebas_Esteban_Sequeda_Actualizadas_18nov2026 §3.

---

### PREGUNTA B2 · ¿El bastión llega al AD por RDP?

**🗣️ ORAL:** "Sí a nivel de red: probé TCP 3389 desde el bastión hasta AD001 y un túnel desde mi Mac que devolvió succeeded. La sesión de Windows depende de credenciales autorizadas; yo no inicié sesión, el consolidado reporta RDP funcionando con `LANGUSTINO\ssdeti`."

**🖥️ EQUIPO:** `[BASTION]` y luego `[MAC]`.

**⌨️ COMANDOS (🟢):**
```bash
# [BASTION]
timeout 5 bash -c 'echo > /dev/tcp/10.1.100.100/3389' && echo 'RDP accesible'
```
```bash
# [MAC] terminal 1 (🟡 abre puerto local)
ssh -o ExitOnForwardFailure=yes -N -L 13389:10.1.100.100:3389 bastion
# [MAC] terminal 2
nc -vz 127.0.0.1 13389
```

**✅ RESULTADO:** `RDP accesible` · `Connection to 127.0.0.1 port 13389 [tcp/*] succeeded!`

**⚠️ SI FALLA:** `bind: Address already in use` → usar 13390. `Broken pipe` → reabrir. No tocar NSG.

**📌 ESTADO:** ✅ conectividad · ⏳ RDP autenticado de Esteban.

**📚 FUENTE:** guía Esteban §3, §8; consolidado prueba 6.

---

### PREGUNTA B3 · ¿Nicolás puede entrar al bastión?

**🗣️ ORAL:** "Su cuenta está en AllowUsers y tiene `.ssh` y `authorized_keys` con permisos correctos: 750 la carpeta personal, 700 `.ssh`, 600 la llave. No probamos su login en vivo."

**⌨️ COMANDO (🟢, permisos, NO contenido):** `sudo ls -ld /home/nicolas /home/nicolas/.ssh /home/nicolas/.ssh/authorized_keys`

**✅ RESULTADO:** `drwxr-x---` … `drwx------` … `-rw-------`.

**📌 ESTADO:** ✅ permisos · ⏳ login de Nicolás.

---

### PREGUNTA B4 · ¿Quién entró al bastión? (auditoría)

**🗣️ ORAL:** "Cada entrada queda en `auth.log` del bastión y además el agente Wazuh 001 envía esos eventos al SIEM; en la ventana de 30 min hubo 29 alertas del bastión."

**⌨️ COMANDO (🟢 ◇ sugerido):** `sudo grep -E "Accepted publickey|Failed" /var/log/auth.log | tail -n 10`

**📌 ESTADO:** Wazuh ✅; el grep es sugerido (no documentado).

---

### ¿Por qué un bastión y no SSH directo / Azure Bastion?
- **Superficie mínima:** ningún servidor publica 22/3389; solo BAS001 es origen permitido (reglas 170/175).
- **Un solo punto auditable** (auth.log + Wazuh).
- **Azure Bastion** descartado por costo en suscripción de estudiante.
- **Riesgo:** punto único; si cae, alternativa `az vm run-command` desde Cloud Shell (usada por Escobar para leer logs de VPN001).
