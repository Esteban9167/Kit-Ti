# 05 · NTP (REQ-ADM-04)

[⬅ Índice Esteban](00_INDICE_ESTEBAN.md) · [NTP no sincroniza](../ESCENARIOS_DE_FALLO/07_NTP_NO_SINCRONIZA.md)

**Flujo:** Hyper-V **PHC0** (referencia de Azure) → DKR001 chrony / contenedor **SRVD1DEVNTP001** (10.1.100.41 **UDP 123**) → Bastión, DKR002, SIEM001.

**Configuración en clientes:** `/etc/chrony/sources.d/langustino.sources` con `server 10.1.100.41 iburst prefer`; aplicado con `chronyc reload sources` y `burst 4/4`, **conservando PHC0** como respaldo.

**NSG:** cu 125/126/127 (UDP 123 desde 10.1.120.10, 10.1.100.102, 10.1.100.101 → 10.1.100.41).

| Cliente | Resultado |
|---|---|
| DKR002 10.1.100.102 | `^* 10.1.100.41` · Reference ID 10.1.100.41 · Stratum 2 · Leap Normal · 0.000000066 s slow |
| SIEM001 10.1.100.101 | `^* 10.1.100.41` · Reach 377 · PHC0 no seleccionado |
| Bastión 10.1.120.10 | `^* 10.1.100.41` · Stratum 2 · Leap Normal · 0.000000008 s slow |
| AD001 10.1.100.100 | Respondió a `chronyd -Q` desde DKR002, diferencia **0.000894 s**. **Fuente Windows y rol PDC no verificados** |

---

### PREGUNTA T1 · ¿Cómo demuestran que el NTP funciona?
**🗣️ ORAL:** "Tenemos un NTP interno en DKR001. Tres clientes Linux lo tienen como fuente permanente y lo seleccionaron: el asterisco junto a 10.1.100.41 indica la fuente activa, con estado Leap Normal."

**👤** Esteban · **🖥️** `[DKR002]` (o `[BASTION]`, `[SIEM001]`) · **🔑** `ssh dkr2`

**⌨️ (🟢):**
```bash
chronyc sources -v
chronyc tracking
cat /etc/chrony/sources.d/langustino.sources
```

**✅** línea `^* 10.1.100.41` · `Reference ID : … (10.1.100.41)` · `Stratum : 2` · `Leap status : Normal`.

**⚠️** Si aparece `^?` o `^x` → ver escenario 07; no reiniciar chrony en vivo.

**📌** ✅ · **📚** guía Esteban §5.

### PREGUNTA T2 · ¿AD001 usa el NTP de DKR001?
**🗣️ ORAL:** "No lo afirmo. AD001 respondió una consulta NTP con 0,894 ms de diferencia, pero eso solo prueba que responde. Para saber su fuente hace falta `w32tm /query /source` y confirmar si es el PDC Emulator; no tuvimos acceso autorizado para hacerlo."

**⌨️ evidencia (🟢):** `[DKR002]` `sudo chronyd -Q -t 15 'server 10.1.100.100 iburst'` (consulta, no ajusta el reloj).

**⌨️ pendiente (🟢, solo con RDP autorizado):** `[AD001/POWERSHELL]` `w32tm /query /source; w32tm /query /status; w32tm /query /configuration; netdom query fsmo`.

**📌** ⏳.

### PREGUNTA T3 · ¿Por qué importa NTP para Kerberos y el SIEM?
"Kerberos rechaza tickets con más de 5 minutos de desfase por defecto; sin hora común falla la autenticación del dominio. En el SIEM, correlacionar eventos de varias fuentes exige marcas de tiempo coherentes." Ver [PREGUNTAS_TRANSVERSALES/06](../PREGUNTAS_TRANSVERSALES/06_IDENTIDAD_Y_AUTENTICACION.md).

### PREGUNTA T4 · ¿Qué servidor NTP es la referencia de DKR001?
"El reloj PHC del hipervisor de Azure (PHC0), que Azure sincroniza con su infraestructura."

### PREGUNTA T5 · ¿En el diseño, cómo debería sincronizar el dominio?
"Lo correcto en AD es que los miembros sigan la jerarquía del dominio y el PDC Emulator tome hora de una fuente confiable (podría ser 10.1.100.41). El diagrama dice 'el AD conserva su jerarquía de dominio'; la configuración del PDC está pendiente de verificar."

### Servidor (◇ sugerido, 🟢)
```bash
# [DKR001]
sudo docker ps --filter name=SRVD1DEVNTP001
sudo ss -ulpn | grep ':123 '
```
