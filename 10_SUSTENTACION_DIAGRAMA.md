# 10 · Sustentación del diagrama (4 min · criterios 1 y 4)

[⬅ README](README.md) · [Arquitectura](04_ARQUITECTURA_GENERAL.md) · [NSG](SANTIAGO/02_VNET_SUBREDES_NSG.md)

Fuente: "Guion para sustentar el diagrama de despliegue" (Santiago Escobar, 17-nov) + archivo `C4_LANgustino_Azure_FINAL.drawio`. **Responsable de esta parte: Santiago Escobar** (puede repartirse por bloques).

| Bloque | Tiempo | Señalar en el diagrama | Qué decir |
|---|---|---|---|
| 1 Visión general | 40 s | Título y las tres cajas punteadas de región | "Despliegue de desarrollo de LANgustino en Azure, grupo `rg-langustino-dev`. 9 VM corporativas en tres regiones —Central US, North Central US y Chile Central— unidas por peering en malla. Tres regiones porque la suscripción de estudiante da 6 vCPU por región. Los servicios livianos corren en 21 contenedores Docker de rol único dentro de DKR001 y DKR002. Aparte, una VM de vendedor remoto en Brasil, en una red sin conexión con la nuestra." |
| 2 Zonas y red | 50 s | Colores, un CIDR, una nota de NSG | "Rojo: servidores expuestos (VPN y balanceador LBR001), únicos con IP pública hacia usuarios. Verde: servidores internos sin IP pública. Amarillo: la LAN, el equipo del vendedor. Cada subred con su CIDR, p. ej. DMZ 10.1.110.0/27 y ERP 10.1.100.64/27. Cada región tiene su NSG: la regla 4000 niega todo el tráfico entre redes y solo abrimos flujos explícitos, de una IP a otra y por un puerto, en el NSG del destino." |
| 3 Entrada de usuarios | 60 s | pip-vpn, pip-lbr-dkr001, bastión | "Dos puertas: la VPN OpenVPN UDP 1194, con usuario y clave de AD más TOTP; el servidor de VPN está unido a `langustino.local` y valida contra el AD. Y el balanceador nginx con certificado comodín de Let's Encrypt, solo 80 y 443; por ahí solo se publica el LMS; ERP e intranet devuelven 403 fuera de la red interna. La administración pasa siempre por el bastión, sin IP pública: SSH a cada servidor y túnel RDP al AD." |
| 4 Aplicación y datos | 50 s | ERP001, SQL001, caja DKR002 | "El ERP es Odoo en su propia VM. PostgreSQL en SQL001 guarda también las bases del LMS, CMS y mesa de ayuda; cada app con su usuario y su regla 5432. En DKR002 corren PBX e IVR en Asterisk; el IVR consulta pedidos en la base del ERP con un usuario de solo lectura. Ahí también la intranet Drupal, el correo, la mesa de ayuda y el NVR." |
| 5 Operación y seguridad | 40 s | NOC001, SIE001, notas de monitoreo y tiempo | "Prometheus y Grafana en NOC001 recogen CPU, RAM y disco de los servidores, incluido el AD con windows_exporter. Wazuh en SIE001 recibe eventos de los agentes del bastión y de los dos hosts Docker, más los propios. NTP001 da la hora a los Linux. La salida a Internet de las subredes privadas pasa por el proxy." |
| 6 Limitaciones | 30 s | Nota general izquierda | "DHCP se demuestra en una red Docker aislada porque Azure no deja pasar broadcast entre VM. DKR001 sale a Internet sin pasar por el proxy porque tiene la IP pública del balanceador: excepción declarada. Y el SIEM no tiene indexador por recursos." |

## Preguntas probables tras el diagrama

| Pregunta | Respuesta (≤20 s) |
|---|---|
| ¿Por qué contenedores y no VM? | Cuota y costo. Cada contenedor sigue siendo un servidor de un solo rol; lo crítico o pesado (ERP, BD, AD, SIEM) va en su propia VM. |
| ¿Por qué tres regiones? | Límite de 6 vCPU por región. Las unimos con peering, que no es transitivo, por eso cada flujo entre regiones tiene su regla. |
| ¿Cómo se verifica una IP del diagrama? | Portal o `az vm list -d`; las IP, tamaños y reglas del diagrama salen de ese inventario. |
| ¿Por qué el AD no es contenedor? | AD DS necesita Windows Server en su propia VM y es la base de la autenticación de la VPN y de los equipos del dominio. |
| ¿Qué pasa si se cae DKR001? | Se pierden balanceador, LMS y servicios de red (DNS, NTP, proxy filtro, NOC, PKI, Samba, backups). En DEV lo aceptamos por costo; en producción irían en más de un host. |
| ¿Por qué el diagrama tiene "C4" en el nombre? | Nombre del archivo del equipo; el contenido es un diagrama de despliegue con iconografía Azure. (No profundizar.) |

## Checklist nivel 5 del criterio 1 (contrastado con el PNG del diagrama)

| Requisito | ¿En el diagrama? |
|---|---|
| Iconografía oficial Azure | ✅ (VM, VNet, NSG, IP pública, contenedores) |
| CPU, RAM, disco en todos los nodos | ✅ en las 10 VM |
| VNet y subredes con CIDR | ✅ |
| Tres zonas identificadas | ✅ (rojo/verde/amarillo + leyenda) |
| Región | ✅ (4 regiones) — zona de disponibilidad: no se ve explícita ⚠️ |
| NSG con puertos y protocolos | ✅ (tablas por NSG) |
| Consistente con topología lógica | Depende del `.pkt`/topología de Pulido (⏳ no evidenciada en este kit) |
| Coincide con matriz de aprovisionamiento | ⏳ "pendiente de consolidar equipo" (DOC-01) |
