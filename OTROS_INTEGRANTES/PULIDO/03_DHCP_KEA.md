# 03 · DHCP — Kea (SRVD1DEVDHC001)

[⬅ Índice Pulido](00_INDICE_PULIDO.md)

**Ficha:** Kea DHCPv4 en DKR001 · red Docker aislada **172.30.50.0/24** · servidor 172.30.50.10 · pool .100–.150 · ámbitos VLAN 10, 20, 30, 40, 50, 99 · reservas de ejemplo en VLAN 40 (teléfonos) y 50 (cámaras) · cliente `langustino-cliente-dhcp` → **172.30.50.100**.

### PREGUNTA DH1 · ¿Por qué DHCP en red aislada?
**🗣️ ORAL:** "Azure asigna las IP de las VM y no propaga broadcast DHCP entre ellas; por eso demostramos Kea en una red Docker aislada con las cuatro fases DORA. Las VLAN son ámbitos configurados; no probamos relay."

**🖥️** `[DKR001]` · **⌨️:**
```bash
docker ps --filter name=SRVD1DEVDHC001                                   # 🟢
docker exec langustino-cliente-dhcp ip -4 addr show eth0                 # 🟢 → 172.30.50.100/24
docker exec langustino-cliente-dhcp sh -c "dhclient -r eth0; dhclient -1 -v eth0"   # 🟡 (lease de laboratorio)
```

**✅** `DHCPDISCOVER`, `DHCPOFFER`, `DHCPREQUEST`, `DHCPACK` de 172.30.50.100.

**📌** ✅ DORA · ⏳ PCAP final (`/tmp/dhcp-dora-completo.pcap`) informado, no revisado.
