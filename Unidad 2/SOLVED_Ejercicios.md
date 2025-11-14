### Ejercicio 1
```bash
sudo nft flush ruleset
sudo nft add table inet filter
sudo nft add chain inet filter input '{ type filter hook input priority 0; policy drop; }'
sudo nft add chain inet filter output '{ type filter hook output priority 0; policy drop; }'
sudo nft add chain inet filter forward '{ type filter hook forward priority 0; policy drop; }'
sudo nft add rule inet filter input ip saddr 192.168.153.0/24 tcp dport 22 counter accept
sudo nft add rule inet filter output udp dport 53 counter accept
sudo nft add rule inet filter output tcp dport '{ 22, 80, 443 }' counter accept
sudo nft list ruleset
```
### Ejercicio 2
```bash

# Script de configuración de firewall con NFTables
# IP del servidor: 192.168.50.10/24
# Red LAN: 192.168.50.0/24
# Host bloqueado: 192.168.50.20
# IP externa permitida para web entrante: 203.0.113.0/24

# 1️⃣ Limpiar reglas existentes
sudo nft flush ruleset

# 2️⃣ Crear la tabla principal
sudo nft add table inet filter

# 3️⃣ Crear cadenas con política DROP
sudo nft add chain inet filter input '{ type filter hook input priority 0; policy drop; }'
sudo nft add chain inet filter output '{ type filter hook output priority 0; policy drop; }'
sudo nft add chain inet filter forward '{ type filter hook forward priority 0; policy drop; }'

# 4️⃣ Reglas de entrada (INPUT)

# 4.1 Permitir SSH desde LAN
sudo nft add rule inet filter input ip saddr 192.168.50.0/24 tcp dport 22 counter accept

# 4.2 Permitir ICMP (ping) desde LAN
sudo nft add rule inet filter input ip saddr 192.168.50.0/24 icmp type echo-request counter accept

# 4.3 Bloquear y contar paquetes de un host específico (opcional)
sudo nft add rule inet filter input ip saddr 192.168.50.20 counter drop

# 4.4 Permitir tráfico web entrante solo desde IP externa permitida
sudo nft add rule inet filter input ip saddr 203.0.113.0/24 tcp dport '{ 80, 443 }' counter accept

# 5️⃣ Reglas de salida (OUTPUT)

# 5.1 Permitir DNS (UDP y TCP) hacia afuera
sudo nft add rule inet filter output udp dport 53 counter accept
sudo nft add rule inet filter output tcp dport 53 counter accept

# 5.2 Permitir HTTP/HTTPS hacia afuera
sudo nft add rule inet filter output tcp dport '{ 80, 443 }' counter accept

# 5.3 Permitir SSH hacia afuera
sudo nft add rule inet filter output tcp dport 22 counter accept

# 5.4 Contar cualquier otro paquete de salida que sea bloqueado
# Esto lo hace la política DROP de la cadena output con contador implícito

# 6️⃣ Verificación de reglas y contadores
sudo nft list ruleset
```