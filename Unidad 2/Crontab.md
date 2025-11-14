# 🕒 Guía Completa de **Crontab**


## 📘 ¿Qué es **crontab**?

**Crontab** (abreviatura de *cron table*) es una utilidad de Linux que permite **programar tareas automáticas** que se ejecutan en momentos específicos.  
Estas tareas son gestionadas por el **demonio `cron`**, que revisa los archivos de configuración y ejecuta los comandos o scripts cuando corresponde.

> En Kali Linux, `cron` viene instalado y activado por defecto.

---

## ⚙️ Verificación e instalación

### Verificar si el servicio `cron` está instalado:

```bash
dpkg -l | grep cron
````

### Instalarlo (si no está presente):

```bash
sudo apt install cron
```

### Habilitar y verificar el servicio

```bash
sudo systemctl enable cron
sudo systemctl start cron
sudo systemctl status cron
```

> Debe mostrarse como **active (running)**.

---

## 📂 Archivos y ubicaciones importantes

| Archivo / Directorio        | Descripción                                   |
| --------------------------- | --------------------------------------------- |
| `/etc/crontab`              | Archivo principal del sistema (requiere root) |
| `/etc/cron.d/`              | Archivos de configuración adicionales         |
| `/etc/cron.daily/`          | Scripts que se ejecutan cada día              |
| `/etc/cron.hourly/`         | Scripts que se ejecutan cada hora             |
| `/etc/cron.weekly/`         | Scripts semanales                             |
| `/etc/cron.monthly/`        | Scripts mensuales                             |
| `/var/spool/cron/crontabs/` | Archivos de tareas de cada usuario            |

---

## 👤 Tipos de crontab

| Tipo                         | Descripción                                          |
| ---------------------------- | ---------------------------------------------------- |
| **Sistema (`/etc/crontab`)** | Gestiona tareas del sistema y de todos los usuarios. |
| **Usuario (`crontab -e`)**   | Cada usuario puede definir sus propias tareas.       |

---

## 🧩 Sintaxis básica de crontab

La sintaxis de una línea de crontab es:

```
MIN HORA DÍA MES DÍA_SEMANA COMANDO
```

| Campo        | Descripción                 | Valores válidos                     |
| ------------ | --------------------------- | ----------------------------------- |
| `MIN`        | Minuto                      | 0–59                                |
| `HORA`       | Hora del día                | 0–23                                |
| `DÍA`        | Día del mes                 | 1–31                                |
| `MES`        | Mes                         | 1–12 o nombres (`jan`, `feb`, etc.) |
| `DÍA_SEMANA` | Día de la semana            | 0–7 (0 o 7 = domingo)               |
| `COMANDO`    | Comando o script a ejecutar | Ruta absoluta o comando shell       |

### Ejemplo:

```bash
30 6 * * * /home/user/scripts/backup.sh
```

➡️ Ejecuta el script **backup.sh** todos los días a las **6:30 a.m.**

---

## ⏰ Operadores especiales

| Símbolo | Significado                | Ejemplo                            |
| ------- | -------------------------- | ---------------------------------- |
| `*`     | Todos los valores posibles | `* * * * *` → Cada minuto          |
| `,`     | Lista de valores           | `0,15,30,45 * * * *` → Cada 15 min |
| `-`     | Rango                      | `1-5` → De lunes a viernes         |
| `/`     | Paso                       | `*/10 * * * *` → Cada 10 minutos   |

---

## 🕹️ Comandos básicos de crontab

| Comando                      | Descripción                           |
| ---------------------------- | ------------------------------------- |
| `crontab -e`                 | Editar las tareas del usuario actual  |
| `crontab -l`                 | Listar las tareas actuales            |
| `crontab -r`                 | Eliminar todas las tareas del usuario |
| `sudo crontab -e`            | Editar las tareas del usuario root    |
| `sudo crontab -l -u usuario` | Ver las tareas de otro usuario        |

---

## 🧠 Variables útiles en crontab

Estas variables se pueden definir al inicio del archivo `crontab`:

| Variable | Descripción                   | Ejemplo                                                             |
| -------- | ----------------------------- | ------------------------------------------------------------------- |
| `SHELL`  | Shell por defecto             | `SHELL=/bin/bash`                                                   |
| `PATH`   | Rutas de búsqueda de comandos | `PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin` |
| `MAILTO` | Enviar salida por correo      | `MAILTO=root` o vacío para desactivar (`MAILTO=""`)                 |
| `HOME`   | Directorio base               | `HOME=/home/user`                                                   |

Ejemplo:

```bash
SHELL=/bin/bash
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin
MAILTO=""
HOME=/home/kali
```

---

## 🧩 Ejemplos prácticos

### 🔁 Ejecutar un script cada minuto

```bash
* * * * * /home/kali/scripts/test.sh
```

---

### 🕕 Ejecutar todos los días a las 6:00 a.m.

```bash
0 6 * * * /home/kali/scripts/backup.sh
```

---

### 💻 Ejecutar un script los lunes a las 9:00 a.m.

```bash
0 9 * * 1 /home/kali/scripts/reporte.sh
```

---

### 📅 Ejecutar el día 1 de cada mes a las 00:00

```bash
0 0 1 * * /home/kali/scripts/mensual.sh
```

---

### ⚡ Ejecutar cada 5 minutos

```bash
*/5 * * * * /home/kali/scripts/check.sh
```

---

### 🔒 Reiniciar un servicio automáticamente

```bash
*/30 * * * * sudo systemctl restart ssh
```

> ⚠️ Si el comando requiere privilegios, asegúrate de que el usuario tenga permisos en `sudoers` sin requerir contraseña.

---

## 📜 Redirigir salida de cron

Por defecto, `cron` envía la salida estándar y de error por correo al usuario.
Puedes redirigirla a un archivo log:

```bash
0 2 * * * /home/kali/scripts/backup.sh >> /var/log/backup.log 2>&1
```

* `>>` → Añade al archivo (sin sobrescribir).
* `2>&1` → Redirige los errores al mismo log.

---

## 🔍 Ver logs y depuración

Ver el log del sistema (cron):

```bash
sudo cat /var/log/syslog | grep CRON
```

En algunas distribuciones (como Kali), también puede usarse:

```bash
sudo journalctl -u cron
```

---

## 🧱 Crontab del sistema (`/etc/crontab`)

El formato del **crontab del sistema** añade una columna extra para el usuario:

```
MIN HORA DÍA MES DÍA_SEMANA USUARIO COMANDO
```

Ejemplo:

```bash
30 1 * * * root /usr/local/bin/limpieza.sh
```

---

## 🧰 Automatización avanzada con `cron.d`

Puedes crear tareas personalizadas del sistema colocando archivos en:

```
/etc/cron.d/
```

Ejemplo:

Archivo `/etc/cron.d/actualizacion`:

```bash
0 3 * * * root apt update && apt upgrade -y
```

Dar permisos adecuados:

```bash
sudo chmod 644 /etc/cron.d/actualizacion
```

---

## 🕵️ Seguridad y buenas prácticas

✅ Usa rutas absolutas (no relativas).
✅ Verifica permisos de ejecución de tus scripts.
✅ Redirige logs para evitar correos innecesarios.
✅ No uses `sudo` dentro de crontab de root.
✅ Testea el script manualmente antes de programarlo.

---

## 🧪 Ejemplo completo — Tareas de mantenimiento en Kali

```bash
SHELL=/bin/bash
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin
MAILTO=""

# Limpieza temporal diaria
0 1 * * * root /usr/local/bin/cleanup.sh >> /var/log/cleanup.log 2>&1

# Actualización semanal (domingos a las 3 AM)
0 3 * * 0 root apt update && apt upgrade -y >> /var/log/update.log 2>&1

# Respaldo mensual
0 0 1 * * root /usr/local/bin/backup.sh >> /var/log/backup.log 2>&1
```

---

## 🚀 Probar tareas cron manualmente

Puedes forzar la ejecución para pruebas con:

```bash
bash -x /ruta/del/script.sh
```

O simular cron con:

```bash
run-parts --test /etc/cron.daily
```

---

## 🧩 Alternativas modernas a cron

* **systemd timers** → Más precisos, con registro integrado en `journalctl`.
* **anacron** → Ejecuta tareas perdidas cuando el sistema estuvo apagado.

---

## 🧭 Conclusión

Crontab es una herramienta esencial para la **automatización en Kali Linux**, permitiendo ejecutar tareas repetitivas de forma segura y sin intervención humana.

> Ideal para tareas de mantenimiento, escaneo automatizado, backups o monitoreo.

