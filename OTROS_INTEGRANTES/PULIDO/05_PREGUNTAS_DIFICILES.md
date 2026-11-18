# 05 · Preguntas difíciles — Pulido

[⬅ Índice Pulido](00_INDICE_PULIDO.md)

| Nivel | Pregunta | Respuesta |
|---|---|---|
| Básica | ¿Qué es un proxy de navegación? | Intermediario que aplica listas y registra cada solicitud. |
| Intermedia | ¿Por qué no inspecciona HTTPS? | Requeriría interceptar TLS con una CA propia en los clientes; decidimos filtrar por nombre (CONNECT). |
| Intermedia | ¿PXY001 o PRX001? | PXY001 = salida a Internet de subredes; PRX001 = filtro de empleados probado (10.1.100.41:3128). |
| Intermedia | ¿Por qué BIND9? | Estándar, autoritativo y recursivo; frente a Unbound (solo recursivo) y CoreDNS (orientado a Kubernetes). *(Comparación de la matriz C1–C5, en consolidación.)* |
| Intermedia | ¿Por qué Kea? | Sucesor de ISC DHCP (fin de soporte), configuración JSON; frente a dnsmasq. |
| Avanzada | ¿DNS003 es el autoritativo público? | No; es laboratorio. DuckDNS responde al público; no hay delegación probada. |
| Avanzada | ¿El AD se resuelve desde DNS001? | Aún no; falta el reenvío condicional de `langustino.local` a 10.1.100.100. |
| Crítica | ¿El correo funciona de punta a punta? | Envío interno reportado; URL del webmail y rechazo externo por confirmar en vivo. |
