# 🔐 Política de Contraseñas en Sistemas Linux

Establecer una **política de contraseñas** en sistemas **Unix/Linux** no es tan directo como en Windows.  
En Linux moderno (por ejemplo, **Ubuntu** o **Kali Linux**), la configuración se reparte entre los siguientes componentes:

- **PAM (Pluggable Authentication Modules)**: módulos configurables ubicados en `/etc/pam.d/`
- **Archivo `/etc/login.defs`**: define parámetros globales de cuentas y contraseñas

A continuación, se detallan los aspectos principales de la configuración.

---

## ⚙️ 1. Longitud mínima de contraseña

Para establecer la **longitud mínima**, edita el archivo:

```bash
sudo nano /etc/pam.d/common-password
```

Busca la línea que contiene `pam_unix.so` y añade al final el parámetro:

```bash
minlen=8
```

Por ejemplo:

```bash
password    requisite     pam_unix.so nullok obscure sha512 minlen=8
```

> 💡 También puedes usar el módulo `pam_pwquality.so` para reglas más avanzadas, como mezcla de mayúsculas, números o símbolos:
>
> ```bash
> password requisite pam_pwquality.so retry=3 minlen=10 ucredit=-1 lcredit=-1 dcredit=-1 ocredit=-1
> ```

---

## ⏳ 2. Caducidad de contraseñas (para nuevas cuentas)

Edita el archivo `/etc/login.defs` con privilegios de administrador:

```bash
sudo nano /etc/login.defs
```

Configura los siguientes parámetros:

| Parámetro       | Descripción                              | Ejemplo              |
| --------------- | ---------------------------------------- | -------------------- |
| `PASS_MAX_DAYS` | Días máximos de validez de la contraseña | `PASS_MAX_DAYS   90` |
| `PASS_MIN_DAYS` | Días mínimos antes de permitir un cambio | `PASS_MIN_DAYS   1`  |
| `PASS_WARN_AGE` | Días de aviso antes de la expiración     | `PASS_WARN_AGE   7`  |

---

## 👤 3. Caducidad para cuentas existentes

Para modificar la política de caducidad de **usuarios ya creados**, usa el comando `chage`:

```bash
sudo chage prometeo
```

El sistema solicitará parámetros como:

* Días máximos y mínimos
* Fecha del último cambio
* Fecha de expiración

Puedes verificar el estado actual con:

```bash
sudo chage -l prometeo
```

O de forma alternativa:

```bash
sudo passwd -S prometeo
```

> ⚠️ Nota: Estas restricciones solo aplican cuando el cambio de contraseña lo realiza un **usuario no privilegiado**.
> El usuario **root** puede establecer cualquier contraseña sin limitaciones.

---

## 🧩 4. Ejemplo práctico

1. **Comprobar el estado de caducidad del usuario `prometeo`:**

```bash
sudo chage -l prometeo
```

2. **Establecer una caducidad máxima de 7 días:**

```bash
sudo chage -M 7 prometeo
```

3. **Configurar la longitud mínima de contraseña:**

```bash
sudo nano /etc/pam.d/common-password
```

Añade al final de la línea que contiene `pam_unix.so`:

```bash
minlen=8
```

4. **Probar el cambio de contraseña con una clave demasiado corta:**

```bash
passwd prometeo
```

El sistema debería **rechazarla** si no cumple con la política establecida.

---

## 🧠 Recomendaciones 

* Utiliza **contraseñas de al menos 12 caracteres**, con letras mayúsculas, minúsculas, números y símbolos.
* Considera activar **bloqueo de cuenta temporal** tras varios intentos fallidos mediante `pam_tally2` o `pam_faillock`.
* Documenta las políticas en `/etc/security/` o en tu sistema de gestión de usuarios centralizado.

```

