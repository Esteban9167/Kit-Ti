# 01 · ERP Odoo 17 + PostgreSQL (ERP001 / SQL001)

[⬅ Índice Daniel](00_INDICE_DANIEL.md) · [Web no carga](../ESCENARIOS_DE_FALLO/11_APLICACION_WEB_NO_CARGA.md)

| VM | IP | Subred | Tamaño | Servicio |
|---|---|---|---|---|
| SRVD1DEVERP001 | 10.1.100.70 | North Central US · snet-erp 10.1.100.64/27 | B2als_v2 2/4 GiB/32 GB | Odoo 17 :8069 |
| SRVD1DEVSQL001 | 10.1.100.71 | ídem | B2als_v2 2/4 GiB/32 GB | PostgreSQL 14 :5432 — bases `langustino` (ERP), `lms`, `cms`, `msa` |

**Integraciones:** LBR001 → ERP001:8069 (NSG nc 135) · VPN → 8069 (130) · gestión → 8069 (140) · ERP001 → SQL001:5432 (150) · DKR002 (IVR `ivr_ro`, CMS, MSA) → SQL001 (155) · DKR001 (LMS, BKP) → SQL001 (158).

**Datos de prueba:** pedido **S00001**, cliente *Cliente Prueba LANgustino*, VAT **900123456**, estado `sale` (Sales Order).

### PREGUNTA E1 · ¿Dónde está el ERP y cómo se llega?
**🗣️ ORAL:** "Odoo 17 corre en su propia VM, ERP001, en la subred de ERP de North Central US. Su base está en SQL001. Los usuarios llegan solo por la VPN a través del balanceador con HTTPS; Odoo escucha en 8069 y solo acepta al balanceador, la VPN y la red de gestión."

**🖥️** `[NAVEGADOR]` https://erp.langustino-lab.duckdns.org (VPN + hosts) · SSH `[MAC]` `ssh -J bastion ssdeti@10.1.100.70`

**⌨️ (◇ 🟢):** `[ERP001]` `sudo ss -lntp | grep 8069` · `[SQL001]` `sudo -u postgres psql -c '\l'`

**📌** ✅ (pruebas 1 y 7).

### PREGUNTA E2 · ¿Qué ocurre si falla PostgreSQL?
**🗣️ ORAL:** "SQL001 es punto único de falla para ERP, LMS, CMS, mesa de ayuda e IVR. El IVR tiene timeout de 3 segundos y dice 'sistema no disponible'; las webs dan error. La base msa tiene backup cada 15 min; la del ERP aún no tiene respaldo automatizado confirmado."

### PREGUNTA E3 · ¿Por qué la base fuera de la VM del ERP?
"Separación de capas, una sola instancia PostgreSQL para varias aplicaciones con usuario y regla 5432 por aplicación, y respaldos centralizados."

### PREGUNTA E4 · ¿El ERP tiene SSO?
No. Ver [P03](../PRUEBAS_RECORRIDO/P03_SSO_AUTENTICACION.md).
