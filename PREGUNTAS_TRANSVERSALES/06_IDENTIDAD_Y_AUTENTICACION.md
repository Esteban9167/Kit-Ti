# 06 · Identidad y autenticación

[⬅ README](../README.md) · [AD](../NICOLAS/01_ACTIVE_DIRECTORY.md) · [VPN](../SANTIAGO/08_VPN_OPENVPN_TOTP.md) · [P03](../PRUEBAS_RECORRIDO/P03_SSO_AUTENTICACION.md)

| Sistema | Cómo autentica | ¿Contra AD? | Integrante |
|---|---|---|---|
| VPN | PAM: TOTP + sssd | ✅ | Escobar |
| VEN001 | Dominio | ✅ (equipo unido) | Escobar / Nicolás |
| RDP AD001 | `LANGUSTINO\ssdeti` | ✅ | Nicolás |
| Bastión / Linux | Llave pública | ❌ (local) | Esteban |
| Odoo | Cuentas propias | ❌ | Daniel |
| Redmine | Cuentas propias (LDAP 389 RootDSE respondió; LDAPS falló) | ❌ | Nicolás |
| Samba | Cuentas locales | ❌ | Nicolás |
| Correo | Cuentas locales | ❌ (opcional) | Pulido |

### PREGUNTA ID1 · ¿Por qué la mesa de ayuda no equivale a SSO?
Ver [NICOLAS/05 M2](../NICOLAS/05_MESA_AYUDA_REDMINE.md).

### PREGUNTA ID2 · ¿Cómo afecta NTP a Kerberos y al SIEM?
**🗣️ ORAL:** "Kerberos rechaza tickets si el desfase entre cliente y controlador supera 5 minutos por defecto, así que la VPN y el dominio dependen de una hora coherente. En el SIEM, ordenar y correlacionar eventos de cuatro fuentes exige la misma hora. Por eso el NTP central; los Linux ya lo usan, y la fuente del AD está pendiente de verificar."

**👥** Esteban (NTP, SIEM), Nicolás (AD). **🖥️** `[DKR002]` `chronyc tracking` 🟢 · `[AD001]` `w32tm /query /source` (⏳).

### PREGUNTA ID3 · ¿Dónde está el segundo factor?
Solo en la VPN (TOTP). RAD001 (FreeRADIUS + TOTP) existe, probado en Fase 2, no integrado a la VPN.
