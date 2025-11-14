# 🔒 Detección de Configuraciones Inseguras en Linux

Detectar configuraciones inseguras es clave para mantener un sistema Linux seguro.  
Esta guía incluye comandos, ejemplos y herramientas recomendadas.

---

<details>
<summary>1️⃣ Usuarios y privilegios</summary>

### Qué revisar:
- Usuarios con contraseña vacía o débil.
- Usuarios con UID 0 además de root.
- Grupos con privilegios excesivos (ej. sudo sin necesidad).

### Comandos útiles:
```bash
# Listar usuarios y su UID
cat /etc/passwd

# Ver usuarios con UID 0 (privilegios root)
awk -F: '($3 == "0") {print $1}' /etc/passwd

# Revisar grupos de sudo
getent group sudo

```
</details>
<details> <summary>2️⃣ Contraseñas y política de seguridad</summary>

Qué revisar:
- Contraseñas sin caducidad.

- Contraseñas demasiado cortas.

- Intentos fallidos ilimitados.

Comandos:
```bash
# Ver caducidad de usuarios
sudo chage -l <usuario>

# Comprobar política PAM
cat /etc/pam.d/common-password

# Revisar /etc/login.defs para PASS_MAX_DAYS, PASS_MIN_DAYS, PASS_WARN_AGE
cat /etc/login.defs
```
</details>
<details> <summary>3️⃣ Permisos de archivos y directorios críticos</summary>

Qué revisar:
- Archivos sensibles con permisos abiertos (777).
- Directorios de configuración accesibles por usuarios no privilegiados.
- Scripts ejecutables modificables por todos.

Comandos:
```bash
# Buscar archivos con permisos 777
find / -type f -perm 0777 2>/dev/null

# Buscar directorios con permisos 777
find / -type d -perm 0777 2>/dev/null

# Revisar propietarios de archivos críticos
ls -l /etc/passwd /etc/shadow /etc/sudoers
```
</details>
<details> <summary>4️⃣ Servicios y puertos</summary>

Qué revisar:
- Servicios innecesarios corriendo.
- Puertos abiertos no requeridos.
- Servicios ejecutándose como root sin necesidad.

Comandos:
```bash
# Listar servicios activos
sudo systemctl list-units --type=service --state=running

# Ver puertos abiertos
sudo ss -tuln
# o
sudo netstat -tulnp
```
</details>
<details> <summary>5️⃣ Configuración de red</summary>

Qué revisar:
- Interfaces con IP pública innecesaria.
- Configuración insegura de SSH (root login permitido, contraseña simple).
- Protocolos inseguros habilitados (telnet, ftp sin TLS).

Comandos:
```bash
# Revisar configuración SSH
cat /etc/ssh/sshd_config | grep -E "PermitRootLogin|PasswordAuthentication"

# Reiniciar SSH si se cambia
sudo systemctl restart ssh
```
</details>
<details> <summary>6️⃣ Paquetes y actualizaciones</summary>

Qué revisar:
- Software desactualizado con vulnerabilidades conocidas.
- Paquetes instalados innecesarios.
  
Comandos:
```bash
# Debian/Ubuntu/Kali
sudo apt update
apt list --upgradable

# Arch Linux
sudo pacman -Syu

# Snap
snap refresh --list
```
</details>
<details> <summary>7️⃣ Logs y auditoría</summary>
Qué revisar:
- Errores de autenticación recurrentes.
- Cambios sospechosos en archivos críticos.

Comandos:
```bash
# Revisar intentos de login fallidos
sudo journalctl _COMM=sshd | tail -n 50

# Revisar cambios recientes en /etc
sudo find /etc -type f -mtime -7
```
</details>
<details> <summary>8️⃣ Herramientas automáticas</summary>
Herramienta	Función
Lynis	Auditoría de seguridad de sistemas Linux
Tiger	Escaneo de vulnerabilidades y configuraciones inseguras
OpenVAS / Greenbone	Escáner de vulnerabilidades de red
Chkrootkit / rkhunter	Detectar rootkits y malware local

Ejemplo con Lynis:
```bash
sudo apt install lynis
sudo lynis audit system
</details>
<details> <summary>💡 Recomendaciones finales</summary>
Realiza auditorías regularmente.

Documenta todos los hallazgos.

Corrige configuraciones inseguras inmediatamente.

Mantén el sistema y paquetes actualizados.

</details> ```