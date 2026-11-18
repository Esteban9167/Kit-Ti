# 03 · VPN no conecta

[⬅ Plan B](../07_PLAN_B_SI_ALGO_FALLA.md) · [VPN001](../SANTIAGO/08_VPN_OPENVPN_TOTP.md)

| Síntoma | Acción |
|---|---|
| Rechaza credenciales | Clave + 6 dígitos **pegados**; esperar el siguiente código (30 s) |
| Sigue rechazando | Escobar en `[CLOUD SHELL]` 🟢: `az vm run-command invoke -g rg-langustino-dev -n SRVD1DEVVPN001 --command-id RunShellScript --scripts "tail -n 20 /var/log/auth.log" --query "value[0].message" -o tsv` |
| Timeout sin pedir credenciales | VPN001 apagada → portal |
| Red del salón bloquea UDP | Usar hotspot del celular |
| Se desconecta sola | Normal ~cada hora; reconectar |
| AD001 apagada | La VPN no valida la clave (sssd) → encender AD001 |
**Impacto:** sin VPN solo queda la prueba 9 (LMS).
