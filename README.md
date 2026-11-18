# README — Cómo usar este kit BAJO PRESIÓN

**LANgustino · Fase 3 · Sustentación 18 de noviembre de 2026 · Equipo SSDETI**

> Si la profesora acaba de preguntar algo: **abre [01_MAPA_RAPIDO_PREGUNTAS.md](01_MAPA_RAPIDO_PREGUNTAS.md)** y busca la palabra clave (Cmd+F / Ctrl+F). Cada fila te dice: archivo → equipo → comando.

## Las 4 reglas de oro

1. **Mira la etiqueta antes de pegar un comando.** `[MAC]`, `[WINDOWS PC]`, `[BASTION]`, `[DKR001]`, `[DKR002]`, `[SIEM001]`, `[AD001/POWERSHELL]`, `[VEN001/POWERSHELL]`, `[NAVEGADOR/GRAFANA]`, `[AZURE PORTAL]`, `[CLOUD SHELL]`. Nunca pegues un comando en otra máquina.
2. **Solo comandos 🟢 durante la sustentación.** 🟡 cambia estado (solo si la prueba lo exige). 🔴 riesgo alto: **NO** en vivo.
3. **No digas "funciona" si el kit dice PENDIENTE.** Di: "está configurado, falta la prueba X". La rúbrica premia la honestidad técnica en el criterio 4.
4. **Nunca muestres contraseñas, `.env`, claves privadas ni códigos TOTP en pantalla.** Las claves están en el gestor de contraseñas del equipo.

## Cómo está organizado

| Necesito… | Abre |
|---|---|
| Orientarme en 30 s | [00_EMPEZAR_AQUI.md](00_EMPEZAR_AQUI.md) |
| Encontrar una respuesta ya | [01_MAPA_RAPIDO_PREGUNTAS.md](01_MAPA_RAPIDO_PREGUNTAS.md) |
| Entrar a un servidor | [02_RUTA_DE_ACCESO_A_SERVIDORES.md](02_RUTA_DE_ACCESO_A_SERVIDORES.md) |
| IP / puerto / tamaño de VM | [03_INVENTARIO_SERVIDORES_IP_PUERTOS.md](03_INVENTARIO_SERVIDORES_IP_PUERTOS.md) |
| Explicar la arquitectura | [04_ARQUITECTURA_GENERAL.md](04_ARQUITECTURA_GENERAL.md) |
| Saber qué está cumplido de la rúbrica | [05_MATRIZ_RUBRICA_Y_EVIDENCIAS.md](05_MATRIZ_RUBRICA_Y_EVIDENCIAS.md) |
| Copiar un comando | [06_COMANDOS_MAESTROS.md](06_COMANDOS_MAESTROS.md) |
| Algo se cayó | [07_PLAN_B_SI_ALGO_FALLA.md](07_PLAN_B_SI_ALGO_FALLA.md) y [ESCENARIOS_DE_FALLO/](ESCENARIOS_DE_FALLO/01_SERVIDOR_APAGADO.md) |
| Me eligieron 3 servidores (criterio 2) | [08_VERIFICACION_EN_VIVO_3_SERVIDORES.md](08_VERIFICACION_EN_VIVO_3_SERVIDORES.md) |
| Orden de la demo / versión ultracorta | [09_GUION_EMERGENCIA_Y_ULTRACORTO.md](09_GUION_EMERGENCIA_Y_ULTRACORTO.md) |
| Sustentar el diagrama (4 min) | [10_SUSTENTACION_DIAGRAMA.md](10_SUSTENTACION_DIAGRAMA.md) |
| Me sortearon la prueba N | [PRUEBAS_RECORRIDO/](PRUEBAS_RECORRIDO/00_INDICE_PRUEBAS.md) |
| Documento Word de las pruebas 1 a 10 (verificación y paso a paso) | [PRUEBAS_RECORRIDO/Pruebas_de_recorrido_1_a_10_Verificacion_y_paso_a_paso.docx](PRUEBAS_RECORRIDO/Pruebas_de_recorrido_1_a_10_Verificacion_y_paso_a_paso.docx) |
| Componente de un compañero | [ESTEBAN](ESTEBAN/00_INDICE_ESTEBAN.md) · [NICOLAS](NICOLAS/00_INDICE_NICOLAS.md) · [DANIEL](DANIEL/00_INDICE_DANIEL.md) · [SANTIAGO (Escobar)](SANTIAGO/00_INDICE_SANTIAGO.md) · [PULIDO](OTROS_INTEGRANTES/PULIDO/00_INDICE_PULIDO.md) |
| Pregunta que cruza varios sistemas | [PREGUNTAS_TRANSVERSALES/](PREGUNTAS_TRANSVERSALES/12_FLUJOS_ENTRE_SISTEMAS.md) |
| Qué falta de verdad | [REFERENCIAS/PENDIENTES_REALES.md](REFERENCIAS/PENDIENTES_REALES.md) |

## Versión HTML

`KIT_NAVEGABLE.html` (en esta misma carpeta) contiene **todo el kit en un solo archivo con buscador**. Ábrelo con doble clic; funciona sin Internet.

## Leyenda de estados

| Marca | Significado |
|---|---|
| ✅ VERIFICADO | Hay evidencia de comando/captura en los documentos del equipo (con fecha). |
| 🟠 PARCIAL | Una parte probada, otra no. Decir exactamente cuál. |
| ⏳ PENDIENTE | Configurado o planeado, sin prueba. No presentarlo como hecho. |
| ❌ NO IMPLEMENTADO | No existe. Explicar el diseño que se habría usado. |
| ⚠️ CONTRADICCIÓN | Dos documentos dicen cosas distintas. Ver [REFERENCIAS/CAMBIOS_RECIENTES.md](REFERENCIAS/CAMBIOS_RECIENTES.md). |

## Leyenda de riesgo de comandos

| Marca | Tipo | ¿En vivo? |
|---|---|---|
| 🟢 | SEGURO — solo lectura (consultas, logs, `docker ps`, métricas) | Sí |
| 🟡 | CAMBIA ESTADO (carga de CPU temporal, publicar noticia, enviar correo, abrir túnel, reiniciar contenedor) | Solo si la prueba lo exige |
| 🔴 | RIESGO ALTO (NSG, GPO, DNS, dominio, firewall, BD, permisos, credenciales) | **No** |

**Corte de información del kit:** 18-nov-2026, basado en los documentos Word del equipo, la rúbrica v2.0 y el diagrama `C4_LANgustino_Azure_FINAL.drawio` (PNG). Ver [REFERENCIAS/FUENTES_CONSULTADAS.md](REFERENCIAS/FUENTES_CONSULTADAS.md).
