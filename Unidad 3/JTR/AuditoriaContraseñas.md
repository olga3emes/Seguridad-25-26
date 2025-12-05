PRÁCTICA – John The Ripper (JTR Jumbo)


📘 Ejercicio 1 – Cracking de contraseñas de sistema (SHA‑512)

Archivo: hashes_sistema.txt

1.1

Ejecuta John the Ripper con el modo automático para intentar crackear los hashes.
¿Qué comando usas?

1.2

Muestra las contraseñas crackeadas.
¿Qué comando usas?

1.3

¿Qué contraseñas aparecen como crackeadas?

📘 Ejercicio 2 – Ataque de Diccionario (Wordlist)

Archivo: hashes_wordlist.txt

2.1

Lanza John usando la wordlist rockyou.txt.
¿Qué comando usas?

2.2

Muestra las contraseñas crackeadas.

2.3

Escribe las contraseñas descubiertas.

📘 Ejercicio 3 – Fuerza Bruta (Incremental)

Archivo: hashes_incremental.txt

3.1

Lanza un ataque incremental.

3.2

Muestra la contraseña encontrada.

3.3

Explica por qué este ataque suele ser más lento que el de diccionario.

📘 Ejercicio 4 – Ataque por Máscara (Mask Mode)

Archivo: hash_mask.txt
Patrón conocido: 3 letras minúsculas + 1 número

4.1

Lanza un ataque con la máscara ?l?l?d?d.

4.2

Muestra la contraseña encontrada.

📘 Ejercicio 5 – WPA‑PSK

Archivo: wifi_wpa.hash

5.1

Lanza John sobre el hash WPA‑PSK.

5.2

Muestra la clave crackeada.

📘 Ejercicio 6 – Hash NTLM (Windows)

Archivo: ntlm_only.hash

6.1

Crackea el hash usando el formato NTLM.

6.2

Muestra la contraseña encontrada.

📘 Ejercicio 7 – Cracking de un archivo RAR

Archivo: documento.rar

7.1

Convierte el RAR a un archivo hash usando rar2john.

7.2

Crackea la contraseña del archivo RAR.

7.3

Muestra la contraseña encontrada.

📘 Ejercicio 8 – Hash SHA‑256 (Aplicación Web)

Archivo: aplicacion_web.sha256

8.1

Crackea el hash usando wordlist.

8.2

Muestra la contraseña crackeada.