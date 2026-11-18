# 07 · Preguntas difíciles — Nicolás

[⬅ Índice Nicolás](00_INDICE_NICOLAS.md)

## Originales del documento de Nicolás

| Pregunta | Respuesta (≤20 s) |
|---|---|
| ¿Por qué AD va en una VM Windows? | AD DS requiere un Windows Server controlador de dominio; PKI/Samba/Redmine/backup se compactaron en contenedores de rol separado. |
| ¿Qué prueba que una GPO se aplica? | El vínculo y el registro prueban configuración; `gpresult /r` en un equipo de la OU prueba aplicación. |
| ¿Por qué no mover vendedor1 sin confirmar? | La VPN usa esa identidad; moverla o cambiar grupos puede afectar directivas y permisos. |
| ¿LDAP equivale a SSO? | No: LDAP autentica, pero la prueba exige pasar del ERP a la mesa de ayuda sin volver a pedir credenciales. |
| ¿Por qué separar PKI interna de certificados públicos? | La CA interna sirve a sistemas privados; la web pública requiere cadena reconocida. |
| ¿Qué impide al operario leer software? | ACL y cuentas Samba verificadas localmente, más controles de red privada. |
| ¿Qué distingue backup de recuperación? | Restauramos un dump y validamos 58 tablas en una BD temporal. |
| ¿Cumplimos RPO de 15 min para ERP? | No demostrado; solo FIL001 y MSA001. |
| ¿Qué harían si DKR001 se pierde? | El repositorio está en el host; falta copia fuera y plan de DR. |
| ¿Por qué no instalar Windows Exporter directamente? *(histórica)* | Ya está instalado (0.32.1); el reto era transferir el MSI sin abrir Internet al AD. |

## Hipotéticas adicionales

| Nivel | Pregunta | Respuesta |
|---|---|---|
| Intermedia | ¿Qué puertos usa el AD? | 53 DNS, 88 Kerberos, 389 LDAP, 636 LDAPS, 445 SMB, 464 cambio de clave, 3268/3269 catálogo global, 135 + 49152-65535 RPC (reglas 270/280). |
| Intermedia | ¿Qué servicios dependen de DNS del AD? | Unión al dominio (VEN001, VPN001), Kerberos (localiza el KDC por SRV `_ldap._tcp.dc._msdcs`), sssd de la VPN. DNS001 aún no reenvía `langustino.local` al AD (pendiente de Pulido). |
| Avanzada | ¿Qué pasa si AD001 cae? | Falla la autenticación de la VPN (vendedor1) y el RDP con cuenta de dominio; servicios con cuentas locales (Samba, Redmine, Odoo) siguen. No hay segundo DC. |
| Avanzada | ¿Cómo harían LDAPS? | Firmar la CSR `ad001.req` en step-ca con SAN del FQDN del DC, instalarla en el almacén del equipo, distribuir la raíz por GPO y abrir TCP 9000 de AD001 a DKR001 para el proceso. |
| Crítica | ¿Por qué la OU Equipos está vacía si la GPO es para equipos? | Por coordinación con la prueba 2; está declarado como pendiente. |
