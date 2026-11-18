# 05 · MSA001 — Mesa de ayuda Redmine (REQ-COM-04)

[⬅ Índice Nicolás](00_INDICE_NICOLAS.md) · [P03 SSO](../PRUEBAS_RECORRIDO/P03_SSO_AUTENTICACION.md)

**Ficha:** contenedor `SRVD1DEVMSA001` · **Redmine 6.1.6** · DKR002 **10.1.100.102:8081 → 3000** interno · `unless-stopped` · BD externa PostgreSQL **msa** en SQL001 10.1.100.71:5432 (regla NSG nc 155). Proyecto "LANgustino - Mesa de Ayuda". **HTTP interno**, no HTTPS.

### PREGUNTA M1 · Muestre el flujo de un ticket
**🗣️ ORAL:** "Redmine corre en Docker en DKR002 y su base vive en SQL001. El ticket #1 lo creó operario1, lo atendió soporte de primer nivel, se escaló a segundo nivel y se cerró."

**🖥️** `[NAVEGADOR]` con VPN → http://10.1.100.102:8081 → proyecto → ticket **#1** → historial.

**⌨️ (🟢)** `[DKR002]` `cd ~/langustino/msa001 && docker compose -f compose.yml config --quiet && docker compose -f compose.yml ps`

**✅** servicio Up publicando `10.1.100.102:8081->3000/tcp` · ticket #1 cerrado con historial N1→N2.

**📌** ✅.

### PREGUNTA M2 · ¿Por qué la mesa de ayuda no equivale automáticamente a SSO?
**🗣️ ORAL:** "Aunque Redmine llegue a validar contra AD por LDAP, cada aplicación seguiría pidiendo la clave. SSO exige un proveedor de identidad que mantenga la sesión y entregue tokens a Odoo y Redmine. Además, el LDAPS falló en TLS y el FQDN del dominio no resuelve desde DKR002."

**📌** ❌ SSO · 🟠 LDAP 389 RootDSE respondió.

### PREGUNTA M3 · ¿Qué pasa si falla PostgreSQL?
"Redmine deja de funcionar (no lleva BD dentro). La base msa tiene respaldo cada 15 min con restauración probada." Ver [06_BACKUPS_RESTIC.md](06_BACKUPS_RESTIC.md).
