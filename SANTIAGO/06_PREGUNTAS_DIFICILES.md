# 06 · Preguntas difíciles — Santiago Escobar

[⬅ Índice Santiago](00_INDICE_SANTIAGO.md)

## Originales (guion del diagrama y pruebas 2/4/6)

| Pregunta | Respuesta (≤20 s) |
|---|---|
| ¿Por qué contenedores y no VM? | Cuota y costo; cada contenedor es un servidor de un solo rol; lo crítico va en VM. |
| ¿Por qué tres regiones? | 6 vCPU por región; peering no transitivo → una regla por flujo inter-región. |
| ¿Cómo se verifica una IP del diagrama? | Portal o `az vm list -d`. |
| ¿Por qué el AD no es contenedor? | AD DS necesita Windows Server; es la base de la VPN y del dominio. |
| ¿Qué pasa si se cae DKR001? | Se pierden balanceador, LMS y servicios de red; aceptado en DEV por costo. |
| ¿Por qué no RADIUS para la VPN? | RAD001 existe (Fase 2); PAM+sssd ya integraba OpenVPN y dominio con menos piezas; RADIUS requeriría UDP 1812 VPN→DKR001. |
| ¿Limitación de la VPN? | PAM acepta usuarios locales como respaldo; en producción se limitaría al grupo `VPN-Users`. |
| ¿Por qué un bastión? | Único punto de entrada administrativa, sin exponer SSH/RDP, con registro. |

## Hipotéticas

| Nivel | Pregunta | Respuesta |
|---|---|---|
| Intermedia | ¿Cómo están separados públicos y privados? | Dos IP públicas de servicio (VPN, LBR); el resto sin IP pública; regla 4000; LBR con allow/deny por nombre. |
| Avanzada | ¿Por qué no Azure Firewall / Application Gateway? | Costo en suscripción de estudiante; NSG + nginx cubren el alcance DEV. |
| Avanzada | ¿Qué pasa si VPN001 cae? | Nadie entra a la red privada (salvo Cloud Shell `run-command`); solo sigue el LMS público. |
| Avanzada | ¿Cómo escalar? | Más vCPU de cuota → separar contenedores críticos (LBR, SQL) en VM o en servicios PaaS (Azure Database for PostgreSQL), y IaC para redesplegar. |
| Crítica | ¿Por qué DKR001 no sale por el proxy? | Tiene IP pública para el balanceador; excepción declarada y documentada en el diagrama. |
| Crítica | ¿La regla 129 existe? | Propuesta el 17-nov; si no se creó, la prueba 4 desde VEN001 no funciona y se usa el plan B desde DKR001. **Confirmar en portal antes.** |
