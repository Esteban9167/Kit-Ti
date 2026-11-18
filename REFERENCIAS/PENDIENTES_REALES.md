# Pendientes reales (no presentar como hechos)

[⬅ README](../README.md) · [Riesgos](../PREGUNTAS_TRANSVERSALES/11_RIESGOS_Y_LIMITACIONES.md)

## Antes de la sustentación (cierran puntos)

| Prioridad | Pendiente | Dueño | Prueba | Puntos en juego |
|---|---|---|---|---|
| 1 | Ensayar ERP desde VEN001 (`hosts` en VEN001) | Escobar | 2 | 4 |
| 2 | Confirmar/crear regla 129 y ensayar filtro desde VEN001 | Escobar + Pulido | 4, 8 | 5 |
| 3 | Confirmar URL webmail, regla 300 y ensayar correo | Pulido + Escobar | 5 | 3 |
| 4 | Ensayar CMS: noticia + 403 desde celular | Daniel | 10 | 2 |
| 5 | Ensayar pico CPU y "sin Internet" | Esteban | 6 | 3 |
| 6 | Compartir TOTP de vendedor1 / IP en NSG vnd para quien presente | Escobar | 2 | — |
| 7 | Verificar Prometheus Up | Esteban | 6 | — |
| 8 | Encender las 10 VM | Escobar / todos | todas | — |

## Pendientes de diseño (declarar como límites)
SSO (Keycloak/OIDC) · LDAPS · distribución de raíz PKI por GPO · GPO aplicada a clientes y políticas adicionales · backup del ERP y off-host · RTO medido · fuente NTP/PDC de AD001 · Wazuh indexer/dashboard y fuentes VPN/proxy/nginx/AD · prueba de disparo de alerta · grabación continua del NVR · reenvío condicional DNS001→AD · delegación DNS003 · PCAP DHCP revisado · SMB intersubredes · IaC/CI/CD · matriz C1–C5, matriz de aprovisionamiento y `.pkt` consolidados.
