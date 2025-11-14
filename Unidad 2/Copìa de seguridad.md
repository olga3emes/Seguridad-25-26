
# 💾 Copia de Seguridad de MySQL en Kali Linux (con `cron`)

## 🧱 1. Instalar MySQL o MariaDB

Kali Linux no trae MySQL instalado por defecto.  
Puedes instalar **MariaDB**, que es una versión 100% compatible con MySQL.

### Instalar el servidor y cliente

```bash
sudo apt update
sudo apt install mariadb-server mariadb-client -y
````

> Si prefieres el paquete oficial de MySQL:

```bash
sudo apt install mysql-server mysql-client -y
```

---

## 🔐 2. Iniciar y habilitar el servicio

Verifica que el servicio esté corriendo:

```bash
sudo systemctl start mysql
sudo systemctl enable mysql
sudo systemctl status mysql
```

Debería mostrar algo como:

```
Active: active (running)
```

---

## 🧰 3. Configurar acceso seguro (opcional pero recomendado)

Ejecuta el asistente de seguridad:

```bash
sudo mysql_secure_installation
```

Este te permite:

* Definir contraseña de root
* Eliminar usuarios anónimos
* Deshabilitar login remoto del root
* Eliminar la base de datos de prueba
* Recargar los privilegios

---

## 🧩 4. Verificar acceso a MySQL/MariaDB

Accede como root:

```bash
sudo mysql -u root -p
```

Si puedes entrar y listar bases de datos:

```sql
SHOW DATABASES;
```

entonces tu instalación está lista.

---

## 💾 5. Crear un script de respaldo (`backup_mysql.sh`)

Crea un archivo en tu carpeta de scripts:

```bash
sudo mkdir -p /usr/local/bin
sudo nano /usr/local/bin/backup_mysql.sh
```

Pega este contenido:

```bash
#!/bin/bash

# === CONFIGURACIÓN ===
USER="root"
PASSWORD="tu_contraseña"
BACKUP_DIR="/var/backups/mysql"
DATE=$(date +'%Y-%m-%d_%H-%M-%S')

# Crear carpeta si no existe
mkdir -p "$BACKUP_DIR"

# Exportar todas las bases de datos a un archivo .sql comprimido
mariadb-dump -u $USER -p$PASSWORD --all-databases | gzip > "$BACKUP_DIR/backup_mysql_$DATE.sql.gz"

# Eliminar copias de seguridad antiguas (más de 7 días)
find "$BACKUP_DIR" -type f -name "*.gz" -mtime +7 -delete

echo "✅ Copia de seguridad completada: $BACKUP_DIR/backup_mysql_$DATE.sql.gz"
```

Guardar y salir (`Ctrl + O`, `Enter`, `Ctrl + X`).

---

## 🔒 6. Dar permisos de ejecución

```bash
sudo chmod +x /usr/local/bin/backup_mysql.sh
```

---

## 🧠 7. Probar el script manualmente

Ejecuta:

```bash
sudo /usr/local/bin/backup_mysql.sh
```

Si todo está bien, deberías ver algo como:

```
✅ Copia de seguridad completada: /var/backups/mysql/backup_mysql_2025-10-17_14-00-00.sql.gz
```

Y el archivo estará comprimido con gzip:

```bash
ls -lh /var/backups/mysql/
```

---

## 🕒 8. Automatizar la copia de seguridad con `crontab`

Abre el crontab de root:

```bash
sudo crontab -e
```

Agrega esta línea al final:

```bash
0 2 * * * /usr/local/bin/backup_mysql.sh >> /var/log/mysql_backup.log 2>&1
```

> Esto ejecutará la copia de seguridad **cada día a las 2:00 a.m.**

---

## 🧾 9. Ver logs y verificar la ejecución

Después de un día, revisa el log:

```bash
cat /var/log/mysql_backup.log
```

Y verifica que los archivos se están generando:

```bash
ls -lh /var/backups/mysql/
```

---

## 🧹 10. Restaurar una copia de seguridad

Para restaurar una base de datos desde el archivo comprimido:

```bash
gunzip < /var/backups/mysql/backup_mysql_2025-10-17_14-00-00.sql.gz | mysql -u root -p
```

> ⚠️ Esto restaurará **todas las bases de datos**.
> Si solo deseas una específica, modifica el script y usa `--databases nombre_db`.

---

## 🧩 11. Versión simplificada para una sola base de datos

Si solo quieres respaldar una base de datos (por ejemplo `testdb`):

```bash
#!/bin/bash
USER="root"
PASSWORD="tu_contraseña"
DATABASE="testdb"
BACKUP_DIR="/var/backups/mysql"
DATE=$(date +'%Y-%m-%d_%H-%M-%S')

mkdir -p "$BACKUP_DIR"

mysqldump -u $USER -p$PASSWORD $DATABASE | gzip > "$BACKUP_DIR/${DATABASE}_$DATE.sql.gz"
```

---

## 🧠 12. Consejos de seguridad

* Nunca dejes la contraseña en texto plano si el servidor es compartido.
  En su lugar, crea un archivo seguro en `/root/.my.cnf`:

  ```bash
  [client]
  user=root
  password=tu_contraseña
  ```

  Luego, elimina la variable `PASSWORD` del script y usa:

  ```bash
  mysqldump --defaults-extra-file=/root/.my.cnf --all-databases | gzip > ...
  ```

* Verifica los permisos del script y del archivo `.my.cnf`:

  ```bash
  sudo chmod 600 /root/.my.cnf
  sudo chmod 700 /usr/local/bin/backup_mysql.sh
  ```

---

## ✅ Resultado final

Una vez configurado:

* El sistema creará automáticamente un **respaldo diario** de tus bases de datos.
* Los archivos se almacenarán en `/var/backups/mysql/`.
* Los respaldos viejos (de más de 7 días) se eliminarán automáticamente.
* Tendrás logs en `/var/log/mysql_backup.log`.

## ⚠️⚠️⚠️⚠️Ya solo nos quedaría que las copias de respaldo se enviasen fuera del servidor, para prevenir ataques que pudiesen dejar el servidor inutilizado.