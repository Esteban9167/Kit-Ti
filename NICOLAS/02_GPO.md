# 02 · GPO

[⬅ Índice Nicolás](00_INDICE_NICOLAS.md)

**GPO propia:** `LANgustino-Seguridad-Equipos`, vinculada y habilitada en `OU=Equipos,DC=langustino,DC=local`. Ajuste: `InactivityTimeoutSecs` = **900** (DWord) en `HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System`. Versión AD/SYSVOL de equipo 1/1. GPO base: Default Domain Policy y Default Domain Controllers Policy habilitadas (no modificarlas).

### PREGUNTA GPO1 · Demuestre la GPO
**🗣️ ORAL:** "Tenemos una GPO propia vinculada a la OU de Equipos que fija 900 segundos de inactividad. El valor está comprobado en AD y SYSVOL; falta colocar un equipo en esa OU y comprobar la aplicación con gpresult."

**🖥️** `[AD001/POWERSHELL]` · **⌨️ (🟢):**
```powershell
Import-Module GroupPolicy
Get-GPO -All | Select-Object DisplayName,GpoStatus
Get-GPInheritance -Target "OU=Equipos,DC=langustino,DC=local" | Format-List GpoLinks
$gpo = "LANgustino-Seguridad-Equipos"
$key = "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System"
Get-GPRegistryValue -Name $gpo -Key $key -ValueName "InactivityTimeoutSecs" | Select-Object ValueName,Type,Value
Get-GPO -Name $gpo | Select-Object DisplayName,ComputerVersion,ModificationTime
```

**✅** vínculo `LANgustino-Seguridad-Equipos` Enabled · `InactivityTimeoutSecs DWord 900`.

**📌** 🟠 configurada ✅ · aplicada ⏳ (OU Equipos vacía).

### PREGUNTA GPO2 · ¿Diferencia entre GPO configurada y aplicada?
**🗣️ ORAL:** "Configurada significa que existe, tiene valores y está vinculada. Aplicada significa que un equipo dentro del alcance la procesó; se prueba en el cliente con `gpresult /r /scope computer`. Como VEN001 sigue en CN=Computers y no en la OU Equipos, no la recibe todavía."

**⌨️ (en el CLIENTE, no en el DC; ⏳):** `gpresult /r /scope computer`

### PREGUNTA GPO3 · ¿Y las demás políticas del plan?
"Bloquear Panel de control/CMD, restricción de software, contraseñas, bloqueo tras tres intentos y delegación de desbloqueo: **sin evidencia de aplicación**. No simulamos intentos fallidos en cuentas operativas."

### PREGUNTA GPO4 · ¿Por qué no movieron VEN001 a la OU?
"Coordinación: VEN001 y vendedor1 sostienen la prueba 2; mover el equipo cambia sus directivas y podría romperla el día de la sustentación."

### Evidencia HTML (🟡 crea archivo, solo si se pide)
```powershell
Get-GPOReport -Name "LANgustino-Seguridad-Equipos" -ReportType Html -Path "C:\LANgustino-Seguridad-Equipos.html"
```
> 🔴 No editar GPO en vivo.
