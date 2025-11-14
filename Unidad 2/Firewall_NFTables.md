# 🔥 Guía Completa de **NFTables** en Linux



## 🧱 ¿Qué es NFTables?

**Nftables** es un proyecto de **Netfilter** que proporciona filtrado y clasificación de paquetes en Linux.  
Es la evolución de **iptables** y, de hecho, **la reemplaza** (no se pueden mezclar nftables e iptables).

Nftables es capaz de sustituir en un mismo framework a:
- `iptables`
- `ip6tables`
- `arptables`
- `ebtables`

Todo ello bajo el mismo espacio de usuario (`nft`) y con compatibilidad hacia atrás (con sintaxis iptables).

> En **Debian 10** (y posteriores), nftables es el framework por defecto.  
> Aunque la sintaxis por defecto sigue siendo iptables, la base ya es nftables.

---

## ⚙️ Compatibilidad y requisitos

- Compatible con **Kernel Linux 3.13+** (recomendado **4.7+**).  
- Debian 10 usa **Kernel 4.19**, por lo que funciona perfectamente.  
- En Debian 10 y posteriores, nftables **ya está instalado** por defecto.

---

## 🚧 Limitaciones de iptables que se mejoran en nftables

- Evitar duplicidad e inconsistencia en el código fuente.  
- Simplificación para IPv4/IPv6.  
- Mejor rendimiento al actualizar conjuntos de reglas.  
- Sintaxis más limpia e intuitiva.  
- Reemplazo unificado de varias herramientas.

---

## 🌟 Principales características de nftables

- Sintaxis compacta e inspirada en **tcpdump**.  
- Tablas y cadenas **totalmente configurables** (no predefinidas).  
- Desaparecen los “match” (`-m`) y los “target” (`-j`); se usan **expresiones**.  
- Permite **varias acciones en una sola regla**.  
- Contadores opcionales.  
- Gestión unificada de **IPv4 e IPv6**.  
- Soporte nativo de conjuntos de direcciones (**ipset integrado**).

> En nftables, los nombres de tablas y cadenas son arbitrarios, y la administración es mucho más sencilla que en iptables.

---

## 🚀 Puesta en marcha en Debian 10

Instalar el frontend:

```bash
sudo apt install nftables
````

No es necesario reiniciar el sistema.
El archivo principal de configuración es:

```
/etc/nftables.conf
```

Contiene una tabla simple para IPv4/IPv6 llamada **`inet filter`**.

---

## 💾 Guardar y aplicar reglas

Guardar las reglas actuales:

```bash
sudo nft list ruleset > /etc/nftables.conf
```

Aplicar los cambios:

```bash
sudo systemctl restart nftables.service
```

> Todos los comandos deben ejecutarse como **root** o con **sudo**.

---

## 🔍 Comandos básicos de nftables

Listar todas las reglas:

```bash
sudo nft list ruleset
```

---

## 🧩 Familias de nftables

| Familia  | Equivalente en iptables |
| -------- | ----------------------- |
| `ip`     | iptables                |
| `ip6`    | ip6tables               |
| `inet`   | IPv4 e IPv6 a la vez    |
| `arp`    | arptables               |
| `bridge` | ebtables                |

> La familia predeterminada es **ip**.
> Si quieres reglas para IPv4 e IPv6, usa **inet**.
> `inet` **no** funciona para NAT, solo para filtrado.

---

## 🏗️ Crear, listar y borrar tablas

### Crear una tabla

```bash
nft add table [familia] [nombre_tabla]
```

Ejemplos:

```bash
nft add table ip filtrado
nft add table inet filtrado
```

### Listar tablas

```bash
nft list tables
```

Listar las cadenas y reglas de una tabla:

```bash
nft list table [familia] [nombre_tabla]
```

Ejemplo:

```bash
nft list table inet filtrado
```

### Borrar una tabla

Primero vaciarla:

```bash
nft flush table [familia] [nombre_tabla]
```

Luego eliminarla:

```bash
nft delete table [familia] [nombre_tabla]
```

---

## 🔗 Cadenas: qué son y cómo usarlas

Las **cadenas** alojan las reglas.
Hay dos tipos:

* **Cadena normal**: usada como salto (`jump`) entre cadenas.
* **Cadena base**: punto de entrada a la pila de red.

### Crear una cadena normal

```bash
nft add chain [familia] [tabla] [cadena]
```

Ejemplo:

```bash
nft add table inet filtrado
nft add chain inet filtrado cadenaejemplo
```

### Crear una cadena base

```bash
nft add chain [familia] [tabla] [cadena] { type tipo hook hook_name priority prioridad; policy política; }
```

**Tipos posibles:**

* `filter`
* `route`
* `nat`

**Hooks por familia:**

| Familia     | Hooks disponibles                               |
| ----------- | ----------------------------------------------- |
| ip/ip6/inet | prerouting, input, forward, output, postrouting |
| arp         | input, output                                   |
| bridge      | prerouting, input, forward, output, postrouting |

Ejemplo:

```bash
nft add table inet filtrado
nft add chain inet filtrado cadenabase { type filter hook input priority 0; policy accept; }
```

---

## 🛠️ Modificar o eliminar cadenas

### Modificar

```bash
nft chain inet filtrado cadenabase { type filter hook output priority 0; policy drop; }
```

### Borrar

Vaciar reglas:

```bash
nft flush chain [familia] [tabla] [cadena]
```

Eliminar cadena:

```bash
nft delete chain [familia] [tabla] [cadena]
```

---

## ⚡ Reglas: creación y manejo

Las reglas se basan en **expresiones** y se almacenan en **cadenas**.

### Añadir una regla

```bash
nft add rule [familia] [tabla] [cadena] [declaracion]
```

Insertar en la parte superior:

```bash
nft insert rule [familia] [tabla] [cadena] [declaracion]
```

Listar reglas con identificadores (`handle`):

```bash
nft --handle list ruleset
```

Eliminar una regla:

```bash
nft delete rule [familia] [tabla] [cadena] handle [identificador]
```

---

## 🧠 Coincidencias y acciones en nftables

### Coincidencias (matches)

* `meta` → interfaz (`iif`, `oif`, `iifname`, `oifname`)
* `ip` / `ipv6` → `saddr`, `daddr`
* `tcp`, `udp`, `sctp` → `sport`, `dport`
* `ct state` → `new`, `established`, `related`, `invalid`
* `icmp`, `icmpv6` → `type`

### Acciones (targets)

* `accept`
* `drop`
* `queue`
* `continue`
* `return`
* `jump` / `goto` [cadena]

---

## 🧱 Ejemplo de firewall básico con nftables

```bash
nft add table inet firewall
nft add chain inet firewall bloqueo { type filter hook input priority 0; policy accept; }

# Bloquear IP específica
nft add rule inet firewall bloqueo ip saddr 192.168.1.2 counter drop

# Bloquear tráfico TCP y UDP en puerto 80 desde una IP
nft add rule inet firewall bloqueo ip saddr 192.168.1.3 tcp dport 80 drop
nft add rule inet firewall bloqueo ip saddr 192.168.1.3 udp dport 80 drop
```

---

## 🧩 Uso de conjuntos (sets) — reemplazo de ipset

```bash
nft add set inet firewall ips_baneadas { type ipv4_addr; }
nft add element inet firewall ips_baneadas { 192.168.1.2 }
nft add element inet firewall ips_baneadas { 192.168.1.3, 192.168.4.66 }
nft add rule inet firewall bloqueo ip saddr @ips_baneadas counter drop
```

Tipos de elementos admitidos:

* `ipv4_addr`
* `ipv6_addr`
* `ether_addr`
* `inet_proto`
* `inet_service`

---

## 🌐 Source NAT (postrouting)

```bash
nft add table nat
nft add chain nat prerouting { type nat hook prerouting priority 0; }
nft add chain nat postrouting { type nat hook postrouting priority 100; }

# SNAT fijo
nft add rule nat postrouting ip saddr 192.168.1.0/24 oif eth0 snat 1.2.3.4

# SNAT dinámico (masquerade)
nft add rule nat postrouting masquerade
```

---

## 🌍 Destination NAT (prerouting)

```bash
nft add table nat
nft add chain nat prerouting { type nat hook prerouting priority 0; }
nft add chain nat postrouting { type nat hook postrouting priority 100; }

# Redirección de puertos 80 y 443
nft add rule nat prerouting iif eth0 tcp dport { 80, 443 } dnat 192.168.1.120
```

### Redirección (Proxy o MitM)

```bash
nft add rule nat prerouting redirect
nft add rule nat prerouting tcp dport 22 redirect to 2222
```

---

## 🔒 Otros ejemplos útiles

### Control de estado de conexión

```bash
ct state established,related accept
ct state invalid drop
```

### Control ICMP

```bash
ip protocol icmp icmp type { destination-unreachable, router-solicitation, router-advertisement, time-exceeded, parameter-problem } accept
```

### Limitar pings

```bash
ip protocol icmp icmp type echo-request limit rate over 10/second burst 4 packets drop
```

### Limitar intentos SSH

```bash
tcp dport ssh ct state new limit rate 15/minute accept
```

### Ejemplo con salto entre cadenas

```bash
table inet filter {
  chain ssh_server {
    tcp dport ssh accept
  }

  chain input {
    type filter hook input priority 0;
    ip saddr 10.10.2.2/24 jump ssh_server
    drop
  }
}
```

---

## 🧩 Conclusión

Nftables es más potente, compacto e intuitivo que iptables.
Simplifica la gestión de reglas, unifica IPv4 e IPv6 y proporciona un rendimiento superior.


