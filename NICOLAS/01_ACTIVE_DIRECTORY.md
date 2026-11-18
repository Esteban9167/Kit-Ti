# 01 · Active Directory (AD001)

[⬅ Índice Nicolás](00_INDICE_NICOLAS.md) · [AD no responde](../ESCENARIOS_DE_FALLO/09_ACTIVE_DIRECTORY_NO_RESPONDE.md)

**Ficha:** SRVD1DEVAD001 · 10.1.100.100 · Chile Central snet-servicios · B2als_v2 2 vCPU / 4 GiB / 127 GB · Windows Server 2022 · dominio **langustino.local** (NetBIOS **LANGUSTINO**) · LDAP 389, Kerberos 88, DNS 53, RDP 3389 · AD DS, DNS y Netlogon activos.

**NSG r3:** 175 (RDP solo desde bastión) · 270 (TCP 53, 88, 135, 389, 445, 464, 636, 3268, 3269, 49152-65535 desde 10.1.0.0/16) · 280 (UDP 53, 88, 123, 389, 464 desde 10.1.0.0/16) · 290 (9182 desde DKR001).

| Objeto | Evidencia |
|---|---|
| OUs | `OU=Usuarios`, `OU=Equipos` (protegidas contra borrado). OU Equipos **vacía** |
| Grupos | `VPN-Users`, `Administradores-TI` (Global, Security) |
| Usuarios | ssdeti, vendedor1, operario1, soporte1 habilitados; Guest y krbtgt deshabilitados. vendedor1 en `CN=Users` (no se movió para no afectar la VPN) |
| Equipos | AD001 en Domain Controllers; **VEN001 y VPN001 en `CN=Computers`** |

## Entrar
`[MAC]` túnel 13389 + Windows App, o `[WINDOWS PC]` `mstsc /v:localhost:13389` → `LANGUSTINO\ssdeti` (ver [Ruta D](../02_RUTA_DE_ACCESO_A_SERVIDORES.md)). Abrir **PowerShell como administrador**. Confirmar: `$env:COMPUTERNAME` → `SRVD1DEVAD001`.

---

### PREGUNTA AD1 · Muéstreme el dominio y su estructura
**🗣️ ORAL:** "El dominio es langustino.local. Tenemos OUs de Usuarios y Equipos, grupos de seguridad VPN-Users y Administradores-TI y cuentas de prueba operario1, soporte1 y vendedor1, que usa la VPN."

**👤** Nicolás · **🖥️** `[AD001/POWERSHELL]`

**⌨️ (🟢):**
```powershell
Import-Module ActiveDirectory
(Get-ADDomain) | Select-Object DNSRoot,NetBIOSName,DomainMode
Get-ADOrganizationalUnit -Filter * | Select-Object Name,DistinguishedName
Get-ADGroup -Filter "Name -eq 'VPN-Users' -or Name -eq 'Administradores-TI'" | Select-Object Name,GroupScope,DistinguishedName
Get-ADUser -Filter * | Select-Object Name,Enabled,DistinguishedName
Get-ADComputer -Filter * | Select-Object Name,DistinguishedName
```

**✅** `langustino.local` · `LANGUSTINO` · OUs Usuarios/Equipos · operario1/soporte1 `Enabled True` · VEN001/VPN001 en `CN=Computers`.

**📌** ✅ · **📚** Pruebas_Nicolas §3.

### PREGUNTA AD2 · ¿Cómo valida la VPN contra el AD?
"VPN001 está unido al dominio y usa sssd; PAM valida primero el TOTP y luego la clave de vendedor1 contra AD001 (Kerberos/LDAP)." Ver [SANTIAGO/08](../SANTIAGO/08_VPN_OPENVPN_TOTP.md).

### PREGUNTA AD3 · ¿Por qué `Administradores-TI` no da privilegios?
"Un grupo nuevo no tiene permisos hasta que se delega o se agrega a grupos privilegiados; el nombre no concede nada."

### PREGUNTA AD4 · ¿El AD tiene Internet?
"No utilizable. Es un servidor interno; la prueba 6 lo usa como ejemplo con `Test-NetConnection www.google.com -Port 443` → False (⏳ sin ensayo registrado)."

### PREGUNTA AD5 · ¿Qué servicios dependen del AD?
VPN (autenticación), VEN001 (dominio), DNS del dominio, futura LDAPS/SSO de Redmine/Odoo, GPO. Ver [PREGUNTAS_TRANSVERSALES/06](../PREGUNTAS_TRANSVERSALES/06_IDENTIDAD_Y_AUTENTICACION.md).

> 🔴 Nunca en vivo: crear/mover usuarios, cambiar contraseñas, mover VEN001 a OU Equipos, provocar bloqueos.
