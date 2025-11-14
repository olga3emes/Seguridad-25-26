
# 🧱 Guía: Uso de iptables como Firewall en Kali Linux


## 🔹 1. ¿Qué es `iptables`?

`iptables` es una herramienta del kernel de Linux que permite crear reglas para **controlar el tráfico de red**.  
Sirve para **permitir, bloquear o redirigir** paquetes según IP, puerto, protocolo o interfaz.

Kali Linux la incluye por defecto y es ideal para configurar un firewall personalizado.

---

## 🔹 2. Conceptos básicos

`iptables` trabaja con **tablas** y **cadenas (chains)**.

### 🔸 Tablas principales
| Tabla | Uso principal |
|--------|----------------|
| **filter** | Filtrado de paquetes (firewall normal). |
| **nat** | Traducción de direcciones (NAT, redirección). |
| **mangle** | Modificación avanzada de paquetes. |
| **raw** | Excepciones de conexión. |

### 🔸 Cadenas comunes
| Cadena | Función |
|---------|----------|
| **INPUT** | Tráfico *entrante* al sistema. |
| **OUTPUT** | Tráfico *saliente* del sistema. |
| **FORWARD** | Tráfico *reenviado* (router). |

---

## 🔹 3. Ver reglas actuales

```bash
sudo iptables -L -v -n
````

* `-L`: lista las reglas.
* `-v`: muestra detalles (bytes, paquetes).
* `-n`: evita resolver nombres (más rápido).

---

## 🔹 4. Definir políticas por defecto

Bloquear todo y permitir solo lo necesario:

```bash
sudo iptables -P INPUT DROP
sudo iptables -P FORWARD DROP
sudo iptables -P OUTPUT ACCEPT
```

👉 Todo tráfico **entrante** y **reenviado** se bloquea.
👉 Todo tráfico **saliente** se permite.

---

## 🔹 5. Permitir tráfico esencial

Permitir conexiones locales (loopback):

```bash
sudo iptables -A INPUT -i lo -j ACCEPT
```

Permitir respuestas a conexiones establecidas:

```bash
sudo iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
```

---

## 🔹 6. Permitir puertos específicos

Ejemplo: permitir SSH, HTTP y HTTPS:

```bash
sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 443 -j ACCEPT
```

Para UDP (ejemplo: DNS puerto 53):

```bash
sudo iptables -A INPUT -p udp --dport 53 -j ACCEPT
```

---

## 🔹 7. Bloquear IPs específicas

Bloquear una IP:

```bash
sudo iptables -A INPUT -s 203.0.113.45 -j DROP
```

Bloquear un rango de IPs:

```bash
sudo iptables -A INPUT -s 203.0.113.0/24 -j DROP
```

---

## 🔹 8. Redirigir puertos (NAT)

Ejemplo: redirigir el tráfico del puerto 8080 al 80:

```bash
sudo iptables -t nat -A PREROUTING -p tcp --dport 8080 -j REDIRECT --to-port 80
```

---

## 🔹 9. Guardar y restaurar reglas

Guardar las reglas actuales:

```bash
sudo sh -c "iptables-save > /etc/iptables/rules.v4"
```

Restaurar reglas:

```bash
sudo iptables-restore < /etc/iptables/rules.v4
```

Instalar el gestor persistente (recomendado):

```bash
sudo apt install iptables-persistent
sudo netfilter-persistent save
sudo netfilter-persistent reload
```

---

## 🔹 10. Borrar o resetear reglas

Borrar todas las reglas:

```bash
sudo iptables -F
sudo iptables -X
sudo iptables -t nat -F
sudo iptables -t nat -X
```

Restablecer políticas:

```bash
sudo iptables -P INPUT ACCEPT
sudo iptables -P FORWARD ACCEPT
sudo iptables -P OUTPUT ACCEPT
```

---

## 🔹 11. Ejemplo completo de firewall seguro

```bash
# Limpiar reglas
iptables -F
iptables -X

# Políticas por defecto
iptables -P INPUT DROP
iptables -P FORWARD DROP
iptables -P OUTPUT ACCEPT

# Permitir loopback
iptables -A INPUT -i lo -j ACCEPT

# Permitir tráfico establecido
iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT

# Permitir SSH, HTTP, HTTPS
iptables -A INPUT -p tcp --dport 22 -j ACCEPT
iptables -A INPUT -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -p tcp --dport 443 -j ACCEPT

# Bloquear IP maliciosa
iptables -A INPUT -s 203.0.113.45 -j DROP
```

Guardar configuración:

```bash
sudo netfilter-persistent save
```

---

## 🔹 12. Monitorear tráfico

Ver reglas en tiempo real:

```bash
sudo watch -n 2 iptables -L -v -n
```

Registrar paquetes bloqueados:

```bash
sudo iptables -A INPUT -j LOG --log-prefix "Paquete bloqueado: "
```

Logs en:

```
/var/log/syslog
/var/log/kern.log
```

---




---

## 🧰 Recursos útiles

* Documentación oficial: [https://man7.org/linux/man-pages/man8/iptables.8.html](https://man7.org/linux/man-pages/man8/iptables.8.html)
* Guía Debian: [https://wiki.debian.org/iptables](https://wiki.debian.org/iptables)
* Proyecto Netfilter: [https://www.netfilter.org](https://www.netfilter.org)


