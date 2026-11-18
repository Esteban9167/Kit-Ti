# Prueba 3 · Autenticación SSO (grado 4) — ❌ NO IMPLEMENTADA

[⬅ Índice pruebas](00_INDICE_PRUEBAS.md) · [Identidad](../PREGUNTAS_TRANSVERSALES/06_IDENTIDAD_Y_AUTENTICACION.md) · [Redmine](../NICOLAS/05_MESA_AYUDA_REDMINE.md)

**Rúbrica:** "Hay Single Sign-On configurado para la mesa de ayuda y el ERP: quien ya se autenticó en el ERP no vuelve a poner sus credenciales de Directorio Activo."

**Responsables:** Nicolás + Daniel.

## Estado real
Odoo y Redmine (SRVD1DEVMSA001, 10.1.100.102:8081) funcionan, **cada uno con sus propias cuentas**. LDAP de Redmine hacia AD: RootDSE respondió en 389, pero **LDAPS falló en TLS** y el SSO no se terminó. Una cuenta con el mismo nombre en dos sistemas **no es SSO**.

## Demostración honesta (60 s)

| # | Dónde | Hacer | Ver |
|---|---|---|---|
| 1 | `[NAVEGADOR]` con VPN | https://erp.langustino-lab.duckdns.org → login | Odoo abierto |
| 2 | `[NAVEGADOR]` | http://10.1.100.102:8081 | Redmine **vuelve a pedir credenciales** → no hay SSO |

## Qué decir
"Esta prueba no la completamos. ERP y mesa de ayuda están desplegados y funcionan, pero con su propio inicio de sesión. El diseño era un proveedor de identidad, Keycloak, que confíe en el Directorio Activo y entregue tokens OpenID Connect a Odoo y a Redmine; así quien entra al ERP llega a la mesa de ayuda sin volver a escribir la clave. Es el primer pendiente para producción."

## Teoría que suma en criterio 4
- **SSO** = un IdP central autentica una vez y entrega un token (OIDC/SAML) a cada aplicación; sesión compartida.
- **LDAP** = cada aplicación valida la misma clave contra el AD **cada vez**: misma credencial, no misma sesión.
- **Kerberos/IWA** sería otra vía (navegador en dominio), pero requiere clientes unidos y SPN.
- Prerrequisitos que faltaban: LDAPS (certificado del DC con SAN/FQDN, CSR `ad001.req` sin firmar), DNS de `langustino.local` resolviendo desde DKR002, IdP desplegado.

**Calificación esperada:** 0 en la prueba; la explicación ayuda en criterio 4. **No simular.**
