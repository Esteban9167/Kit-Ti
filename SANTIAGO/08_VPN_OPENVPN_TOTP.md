# 08 · VPN001 — OpenVPN + AD + TOTP *(archivo adicional)*

[⬅ Índice Santiago](00_INDICE_SANTIAGO.md) · [VPN no conecta](../ESCENARIOS_DE_FALLO/03_VPN_NO_CONECTA.md)

**Ficha:** SRVD1DEVVPN001 · 10.1.110.10 · pública 52.154.177.199 · snet-dmz · B2pts_v2 2/1 GiB/32 GB · Ubuntu 22.04 ARM64 · **OpenVPN UDP 1194** · clientes **10.8.0.0/24** · NAT a 10.1.110.10 · unido a `langustino.local` (en `CN=Computers`).

**Autenticación (PAM):** 1) `pam_google_authenticator` valida el código TOTP (30 s) · 2) `pam_sss` valida la clave contra AD001 vía sssd. El usuario escribe **clave + código pegados** en el campo contraseña.

### PREGUNTA VPN1 · ¿Cómo funciona el doble factor?
**🗣️ ORAL:** "Algo que se sabe, la clave del Directorio Activo, y algo que se tiene, el código de seis dígitos de la app que cambia cada 30 segundos. PAM comprueba primero el código y luego pregunta la clave al AD por sssd, porque el servidor de VPN está unido al dominio."

**📌** ✅ vendedor1 autenticado (Escobar).

### PREGUNTA VPN2 · ¿Cómo veo por qué rechazó? (🟢)
```bash
# [CLOUD SHELL]
az vm run-command invoke -g rg-langustino-dev -n SRVD1DEVVPN001 --command-id RunShellScript --scripts "tail -n 20 /var/log/auth.log" --query "value[0].message" -o tsv
```
Buscar `vendedor1`, `pam_google_authenticator`, `pam_sss`.

### Notas
- La VPN se cae sola aproximadamente cada hora: reconectar justo antes.
- No hay agente Wazuh en VPN001 (fuente pendiente del SIEM).
