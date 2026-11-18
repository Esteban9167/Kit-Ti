# 02 · PBX001 — Asterisk

[⬅ Índice Daniel](00_INDICE_DANIEL.md)

**Ficha:** contenedor `SRVD1DEVPBX001` en DKR002 · **Asterisk 18** · SIP **UDP/TCP 5060** · RTP **UDP 10000-10049** · extensión **101** (softphone) · enruta **500** al IVR (SIP 5062).

**NSG r3:** 220 (UDP 5060), 230 (UDP 10000-10099), 240 (TCP 5060) desde **10.1.110.10** (VPN) → 10.1.100.102. Configuradas por Escobar.

### PREGUNTA PBX1 · ¿Por qué MicroSIP debe ir por TCP?
"Con UDP la llamada falla ('Solicitud expiró'); por TCP el registro y la señalización atraviesan la VPN/NAT correctamente. Está documentado en la preparación." (Causa raíz exacta no documentada: no inventar.)

### PREGUNTA PBX2 · ¿Por qué solo un dispositivo en la 101?
"La extensión admite un registro: el último que se registra desplaza al anterior. Quien presenta se registra de último."

### Comprobación (◇ 🟢)
```bash
# [DKR002]
sudo docker ps --filter name=SRVD1DEVPBX001
sudo docker exec SRVD1DEVPBX001 asterisk -rx "pjsip show endpoints"   # ◇ si usa chan_pjsip; si usa chan_sip: "sip show peers"
```
