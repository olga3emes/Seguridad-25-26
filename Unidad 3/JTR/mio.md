Perfecto.
Aquí tienes una **PRÁCTICA COMPLETA, RESUELTA, PASO A PASO**, con **preguntas + solución** para cada apartado.
Es ideal para entregar a profesores o usar como material de corrección.

---

# 🧪 **PRÁCTICA RESUELTA – John The Ripper (JTR Jumbo)**

**Preguntas + solución paso a paso**

---

# 📘 **Ejercicio 1 – Cracking de contraseñas de sistema (SHA‑512)**

**Archivo:** `hashes_sistema.txt`

### **PREGUNTA 1.1**

Ejecuta John the Ripper con el modo automático para intentar crackear los hashes.
**¿Qué comando usas?**

### ✔️ **SOLUCIÓN 1.1**

```bash
john hashes_sistema.txt
```

---

### **PREGUNTA 1.2**

Muestra las contraseñas crackeadas.
**¿Qué comando usas?**

### ✔️ **SOLUCIÓN 1.2**

```bash
john --show hashes_sistema.txt
```

---

### **PREGUNTA 1.3**

¿Qué contraseñas aparecen como crackeadas?

### ✔️ **SOLUCIÓN 1.3**

Este archivo usa hashes simulados (no crackeables), por lo que:

✔ **No se crackea ninguna contraseña real.**
Esto se usa para evaluar el comando, no el resultado real.

---

---

# 📘 **Ejercicio 2 – Ataque de Diccionario (Wordlist)**

**Archivo:** `hashes_wordlist.txt`
Incluye hashes de contraseñas: **password, test, foo**

---

### **PREGUNTA 2.1**

Lanza John usando la wordlist rockyou.txt.
**¿Qué comando usas?**

### ✔️ **SOLUCIÓN 2.1**

```bash
john --wordlist=/usr/share/wordlists/rockyou.txt hashes_wordlist.txt
```

---

### **PREGUNTA 2.2**

Muestra las contraseñas crackeadas.

### ✔️ **SOLUCIÓN 2.2**

```bash
john --show hashes_wordlist.txt
```

---

### ✔️ **SOLUCIÓN – RESULTADOS**

| Algoritmo | Contraseña   |
| --------- | ------------ |
| MD5       | **password** |
| SHA1      | **test**     |
| SHA256    | **foo**      |
| NTLM      | **password** |

---

---

# 📘 **Ejercicio 3 – Fuerza Bruta (Incremental)**

**Archivo:** `hashes_incremental.txt`
Contraseña: **abc123**

---

### **PREGUNTA 3.1**

Lanza un ataque incremental por defecto.

### ✔️ **SOLUCIÓN 3.1**

```bash
john --incremental hashes_incremental.txt
```

---

### **PREGUNTA 3.2**

Muestra la contraseña encontrada.

### ✔️ **SOLUCIÓN 3.2**

```bash
john --show hashes_incremental.txt
```

---

### ✔️ **RESULTADO**

Contraseña encontrada: **abc123**

---

### **PREGUNTA 3.3 (reflexión)**

¿Por qué este ataque es más lento que el de wordlist?

### ✔️ **SOLUCIÓN**

Porque prueba **todas las combinaciones posibles**, sin información previa.
Tiene **complejidad exponencial** ⇒ muy costoso.

---

---

# 📘 **Ejercicio 4 – Ataque por Máscara (Mask Mode)**

**Archivo:** `hash_mask.txt`
Patrón conocido: **3 letras minúsculas + 1 número**
Contraseña real: **ab12**

---

### **PREGUNTA 4.1**

Ejecuta un ataque con la máscara `?l?l?d?d`.

### ✔️ **SOLUCIÓN 4.1**

```bash
john --mask='?l?l?d?d' hash_mask.txt
```

---

### **PREGUNTA 4.2**

Muestra la contraseña crackeada.

### ✔️ **SOLUCIÓN 4.2**

```bash
john --show hash_mask.txt
```

---

### ✔️ **RESULTADO**

Contraseña encontrada: **ab12**

---

---

# 📘 **Ejercicio 5 – WPA‑PSK (simulado)**

**Archivo:** `wifi_wpa.hash`
Contraseña real: **miclavewifi**

---

### **PREGUNTA 5.1**

Lanza JTR sobre el hash WPA‑PSK.

### ✔️ **SOLUCIÓN 5.1**

```bash
john wifi_wpa.hash
```

---

### **PREGUNTA 5.2**

Muestra la clave WPA crackeada.

### ✔️ **SOLUCIÓN 5.2**

```bash
john --show wifi_wpa.hash
```

---

### ✔️ **RESULTADO**

Contraseña encontrada: **miclavewifi**

---

---

# 📘 **Ejercicio 6 – Hash NTLM (Windows)**

**Archivo:** `ntlm_only.hash`
Contraseña real: **password**

---

### **PREGUNTA 6.1**

Fuerza el formato NTLM y crackea el hash.

### ✔️ **SOLUCIÓN 6.1**

```bash
john --format=NT hashes_ntlm_only.hash
```

---

### **PREGUNTA 6.2**

Visualiza la contraseña.

### ✔️ **SOLUCIÓN 6.2**

```bash
john --show hashes_ntlm_only.hash
```

---

### ✔️ **RESULTADO**

Contraseña encontrada: **password**

---

---

# 📘 **Ejercicio 7 – Cracking de un archivo RAR**

**Archivo:** `documento.rar`
Contraseña real: **rar123**

---

### **PREGUNTA 7.1**

Extrae el hash del RAR con *rar2john*.

### ✔️ **SOLUCIÓN 7.1**

```bash
rar2john documento.rar > documento.rar.hash
```

---

### **PREGUNTA 7.2**

Crackea el hash del archivo RAR.

### ✔️ **SOLUCIÓN 7.2**

```bash
john documento.rar.hash
```

---

### **PREGUNTA 7.3**

Muestra el resultado.

### ✔️ **SOLUCIÓN 7.3**

```bash
john --show documento.rar.hash
```

---

### ✔️ **RESULTADO**

Contraseña RAR: **rar123**

---

---

# 📘 **Ejercicio 8 – Hash SHA‑256 (Aplicación Web)**

**Archivo:** `aplicacion_web.sha256`
Contraseña real: **web2024**

---

### **PREGUNTA 8.1**

Lanza JTR sobre el hash SHA‑256 con wordlist.

### ✔️ **SOLUCIÓN 8.1**

```bash
john --wordlist=/usr/share/wordlists/rockyou.txt aplicacion_web.sha256
```

---

### **PREGUNTA 8.2**

Visualiza el resultado.

### ✔️ **SOLUCIÓN 8.2**

```bash
john --show aplicacion_web.sha256
```

---

### ✔️ **RESULTADO**

Contraseña crackeada: **web2024**
