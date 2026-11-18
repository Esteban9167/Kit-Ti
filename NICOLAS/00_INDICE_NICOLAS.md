# Nicolás — Índice

[⬅ README](../README.md)

**Componentes:** AD001 (Active Directory + GPO, REQ-SEC-01) · PKI001 (step-ca) · FIL001 (Samba) · MSA001 (Redmine, REQ-COM-04) · BKP001 (Restic). Además instaló **Windows Exporter 0.32.1** en AD001. **Escenas 5 y 6** (empleado / analista de soporte). Pruebas relacionadas: 2 (AD), 3 (SSO), 6 (RDP), 8 (Samba).

| Archivo | Dónde | Estado |
|---|---|---|
| [01_ACTIVE_DIRECTORY.md](01_ACTIVE_DIRECTORY.md) | AD001 10.1.100.100 | ✅ dominio, OUs, grupos, usuarios |
| [02_GPO.md](02_GPO.md) | AD001 | 🟠 vinculada y valor 900 ✅; aplicación en cliente ⏳ |
| [03_PKI_CERTIFICADOS.md](03_PKI_CERTIFICADOS.md) | DKR001:9000 | ✅ /health · ⏳ distribución raíz y LDAPS |
| [04_SERVIDOR_ARCHIVOS.md](04_SERVIDOR_ARCHIVOS.md) | DKR001:445 | ✅ local · ⏳ desde otra subred |
| [05_MESA_AYUDA_REDMINE.md](05_MESA_AYUDA_REDMINE.md) | DKR002:8081 | ✅ ticket #1 · ❌ SSO |
| [06_BACKUPS_RESTIC.md](06_BACKUPS_RESTIC.md) | DKR001 | ✅ FIL + msa, restauración 58 tablas · ⏳ ERP y off-host |
| [07_PREGUNTAS_DIFICILES.md](07_PREGUNTAS_DIFICILES.md) | — | — |
| [08_GUION_DEMOSTRACION.md](08_GUION_DEMOSTRACION.md) | — | — |

**Advertencias de Nicolás:** una cuenta local de Redmine/Samba con el mismo nombre que una de AD **no** demuestra LDAP ni SSO. Una GPO guardada y vinculada **no** demuestra su aplicación. AD001 no tiene Internet utilizable: no repetir scp/portapapeles RDP que ya fallaron. No provocar bloqueos de cuentas.
