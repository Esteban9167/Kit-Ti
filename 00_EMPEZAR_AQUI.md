# 00 · EMPEZAR AQUÍ (léelo en 60 segundos)

[⬅ README](README.md) · [Mapa rápido](01_MAPA_RAPIDO_PREGUNTAS.md) · [Guion ultracorto](09_GUION_EMERGENCIA_Y_ULTRACORTO.md)

## La sesión (30 min, rúbrica v2.0)

| Bloque | Min | Criterio | Archivo |
|---|---|---|---|
| Sustentación del diagrama | 4 | 1 y 4 | [10_SUSTENTACION_DIAGRAMA.md](10_SUSTENTACION_DIAGRAMA.md) |
| Verificación en vivo de 3 servidores elegidos por la profesora | 6 | 2 | [08_VERIFICACION_EN_VIVO_3_SERVIDORES.md](08_VERIFICACION_EN_VIVO_3_SERVIDORES.md) |
| 10 pruebas de recorrido sorteadas (~90 s c/u) | 15 | 3 | [PRUEBAS_RECORRIDO/00_INDICE_PRUEBAS.md](PRUEBAS_RECORRIDO/00_INDICE_PRUEBAS.md) |
| Preguntas técnicas | 5 | 4 | [01_MAPA_RAPIDO_PREGUNTAS.md](01_MAPA_RAPIDO_PREGUNTAS.md) |

**Grupo de 5 → cada integrante hace 2 pruebas.** El sorteo es por prueba, no se reasigna. Cualquiera puede tocar cualquier prueba.

## El proyecto en 5 frases (para decir en voz alta)

1. "LANgustino es el ambiente DEV en Azure, grupo de recursos `rg-langustino-dev`: 9 VM corporativas en 3 regiones (Central US, North Central US, Chile Central) unidas por peering en malla, más 1 VM de vendedor remoto en Brazil South sin peering."
2. "Usamos tres regiones porque la suscripción Azure for Students da 6 vCPU por región."
3. "Los servicios livianos corren en 21 contenedores Docker de rol único, en dos hosts: DKR001 (12) y DKR002 (9)."
4. "Hay dos puertas: la VPN (OpenVPN UDP 1194, usuario de AD + TOTP) y el balanceador nginx LBR001 (solo 80/443, certificado comodín Let's Encrypt). La administración entra siempre por el bastión, que no tiene IP pública."
5. "Cada NSG niega VirtualNetwork→VirtualNetwork con la regla 4000 y solo abre flujos explícitos IP→IP:puerto en el NSG del destino."

## Estado real de las 10 pruebas (resumen)

| # | Prueba | Grado | Estado | Riesgo |
|---|---|---|---|---|
| 1 | IVR | 5 | ✅ probada completa 17-nov | Bajo si VPN+MicroSIP TCP |
| 2 | Vendedor remoto | 4 | 🟠 VPN+AD+TOTP+IP 10.8.0.x ✅; abrir ERP desde VEN001 ⏳ | Medio (TOTP solo en celular de Escobar) |
| 3 | SSO | 4 | ❌ no implementado | Alto: decir la verdad + diseño Keycloak |
| 4 | Filtro navegación | 3 | 🟠 403/200 desde DKR001 ✅; desde VEN001 ⏳ (regla 129 3128) | Medio |
| 5 | Correo | 3 | 🟠 envío interno reportado; URL webmail ⏳; rechazo externo 554 observado antes | Alto |
| 6 | Administración TI | 3 | 🟠 SSH/RDP/Grafana/Wazuh ✅; pico CPU en vivo y "sin internet" sin ensayo registrado | Medio |
| 7 | Certificado | 2 | ✅ | Bajo |
| 8 | Software autorizado | 2 | 🟠 AnyDesk 403 desde DKR001 ✅; desde VEN001 ⏳ | Medio |
| 9 | LMS | 2 | ✅ | Bajo |
| 10 | CMS | 2 | 🟠 configurado; publicar noticia y 403 externo ⏳ (ensayo) | Medio |

Detalle y evidencias: [05_MATRIZ_RUBRICA_Y_EVIDENCIAS.md](05_MATRIZ_RUBRICA_Y_EVIDENCIAS.md).

## Antes de entrar al salón (checklist de 10 min)

- [ ] **[AZURE PORTAL]** Encender: VPN001, BAS001, ERP001, SQL001, DKR001, DKR002, AD001, SIE001, PXY001, SRVD1DEVVEN001 (≈15 min antes).
- [ ] **[MAC]/[WINDOWS PC]** OpenVPN conectado (clave+6 dígitos pegados). La VPN se cae ≈ cada hora: reconectar justo antes.
- [ ] **[MAC]** Terminales: `ssh bastion`, `ssh dkr1`, `ssh dkr2`, `ssh siem`.
- [ ] **[MAC]** Túnel Grafana: `ssh -N -L 3000:127.0.0.1:3000 dkr1` → http://localhost:3000.
- [ ] **[DKR001]** `sudo docker ps --format "table {{.Names}}\t{{.Status}}"` → Prometheus y Grafana **Up** (Prometheus ya estuvo `Exited (0)` una vez).
- [ ] **[WINDOWS PC]** archivo `hosts` con `10.1.100.41 erp.langustino-lab.duckdns.org cms.langustino-lab.duckdns.org`.
- [ ] MicroSIP **En línea** por **TCP** (solo quien vaya a hacer la prueba 1; la ext. 101 admite un solo dispositivo).
- [ ] Celular con datos móviles (Wi-Fi apagado) para pruebas 7 (opcional), 9 y 10.
- [ ] Capturas de respaldo abiertas en una carpeta (ver [07_PLAN_B_SI_ALGO_FALLA.md](07_PLAN_B_SI_ALGO_FALLA.md)).

## Si solo tienes 15 segundos

→ [09_GUION_EMERGENCIA_Y_ULTRACORTO.md#versión-ultracorta](09_GUION_EMERGENCIA_Y_ULTRACORTO.md)
