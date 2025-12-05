#  **Práctica: Auditoría de Contraseñas con John the Ripper (Jumbo Build)**

**Duración estimada:** 1.5–2 horas
**Nivel:** Principiante – Intermedio
**Objetivo general:**
Los alumnos aprenderán a identificar, extraer y auditar hashes de contraseñas utilizando diferentes modos de ataque en John the Ripper.

---

#  **Escenario**

Una empresa te contrata para realizar una **auditoría de contraseñas** de varias fuentes:

* usuarios locales del sistema
* archivo ZIP protegido
* base de datos con hashes de aplicaciones web
* un hash NTLM entregado por el equipo de redes

Tu misión: **recuperar tantas contraseñas como sea posible** y redactar un informe técnico.

---

#  **Parte 1 — Extracción de Hashes del Sistema**

1. Usa `unshadow` para combinar passwd y shadow:

   ```bash
   unshadow passwd shadow > hashes_sistema.txt
   ```

2. Ejecuta un ataque **Single**:

   ```bash
   john --single hashes_sistema.txt
   ```

3. Revisa resultados:

   ```bash
   john --show hashes_sistema.txt
   ```

### **Preguntas a responder**

* ¿Qué contraseñas se recuperaron?
* ¿Por qué funciona tan bien el modo *single*?

---

# **Parte 2 — Ataque con Wordlist a hashes variados**

1. Verifica formato:

   ```bash
   john --list=formats
   ```

2. Lanza un ataque con diccionario:

   ```bash
   john --wordlist=/usr/share/wordlists/rockyou.txt hashes.txt
   ```

3. Vuelve a revisar resultados:

   ```bash
   john --show hashes.txt
   ```

### **Preguntas**

* ¿Qué formatos de hash fueron crackeados?
* ¿Qué tipos resistieron?

---

#  **Parte 3 — Crackeo de un archivo ZIP**

1. Extrae hash con `zip2john`:

   ```bash
   zip2john archivo.zip > zip.hash
   ```

2. Usa wordlist:

   ```bash
   john --wordlist=rockyou.txt zip.hash
   ```

### **Preguntas**

* ¿Qué tan segura era la contraseña del ZIP?
* ¿Cómo se podría mejorar su seguridad?

---

#  **Parte 4 — Ataque de Fuerza Bruta (Incremental)**

1. Ejecuta:

   ```bash
   john --incremental hashes_incremental.txt
   ```

2. Limita longitud:

   ```bash
   john --incremental=Digits --min-length=4 --max-length=6 hashes_incremental.txt
   ```

### **Preguntas**

* ¿Qué impacto tiene la longitud en el tiempo de crackeo?
* ¿Por qué este modo no es práctico para contraseñas largas?

---

#  **Parte 5 — Ataque con Máscara (Mask Attack)**

El equipo de redes te dice que una contraseña olvidada **tiene este patrón**:

> dos letras minúsculas + tres números
> (ejemplo: ab123)

1. Ejecuta:

   ```bash
   john --mask='?l?l?d?d?d' hash_nuevo.txt
   ```

### **Preguntas**

* ¿Qué ventaja tiene este ataque respecto al incremental?
* ¿Por qué reduce drásticamente el tiempo?

---

#  **Parte 6 — Reto Final**

Los alumnos deben elegir el método adecuado para estos casos:

| Archivo / Hash       | Pista                         | Objetivo                     |
| -------------------- | ----------------------------- | ---------------------------- |
| `wifi_wpa.hash`      | Contraseña de 8–10 caracteres | Seleccionar método óptimo    |
| `ntlm_only.hash`     | Hash NTLM                     | Comparar wordlist vs máscara |
| `documento.rar`      | Archivo protegido             | Extraer + crackear           |
| `aplicacion_web.txt` | Hashes SHA-256                | Probar diccionario + reglas  |

Pueden combinar modos, modificar máscaras o crear propias.



