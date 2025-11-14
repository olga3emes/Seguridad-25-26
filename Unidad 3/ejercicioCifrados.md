# 1.	CIFRAR Y DESCIFRAR CON EL CIFRADO CÉSAR (7 DESPLAZAMIENTOS) (BASH)

### Cifrar
echo "Gloria descifra este mensaje" | tr [a-z] [h-zabcdef] | tr [A-Z] [H-ZABCDEF]
### Descifrar
echo "Nsvyph klzjpmyh lzal tluzhql" | tr [h-zabcdef] [a-z] | tr [H-ZABCDEF] [A-Z]

Cifra y descifra: “ La criptografía es muy guay :)”.


# 1️⃣ Cifrar con DES

Vamos a cifrar y descifrar usando DES en Bash. Para esto necesitamos la utilidad openssl, que permite cifrado simétrico con DES.

echo "La criptografía es muy guay :)" | openssl enc -des -base64 -pass pass:miClaveSecreta

Explicación:

enc -des → usa el cifrado DES.

-base64 → convierte la salida a Base64 para que sea legible.

-pass pass:miClaveSecreta → contraseña para cifrar (puedes cambiarla).

Ejemplo de salida cifrada:

U2FsdGVkX1+fO1YqzEo1H2lROyXcQyPq7vXDC8CwHhM=


Nota: la salida cambia cada vez porque DES genera un vector de inicialización aleatorio.

# 2️⃣ Descifrar con DES
echo "U2FsdGVkX1+fO1YqzEo1H2lROyXcQyPq7vXDC8CwHhM=" | openssl enc -des -d -base64 -pass pass:miClaveSecreta


Explicación:

-d → descifrar.

-base64 → convierte de Base64 a bytes antes de descifrar.

-pass pass:miClaveSecreta → misma contraseña usada para cifrar.

Resultado descifrado:

La criptografía es muy guay :)

## Por qué DES es inseguro

DES usa una clave de 56 bits, lo que hoy es extremadamente pequeño.
Un atacante moderno puede probar todas las claves posibles (búsqueda exhaustiva) utilizando hardware especializado o computación distribuida.

En 1998

La EFF construyó una máquina llamada Deep Crack que rompía DES en unas 56 horas.

Hoy

Un clúster de GPUs puede romper DES en minutos u horas.

El ataque no requiere matemáticas avanzadas, solo fuerza bruta… por eso el algoritmo está deprecado desde hace décadas.

### 🔍 ¿Cómo se rompe en teoría?

Sin instrucciones prácticas, solo la idea conceptual:

#### Ataque por fuerza bruta

Se toma el mensaje cifrado.

Se prueba una clave DES.

Se descifra.

Si el resultado tiene “sentido”, se asume que la clave es correcta.

Se repite hasta encontrarla.

Con 2⁵⁶ claves ≈ 7.2×10¹⁶ intentos, esto es viable con máquinas actuales.

# 🔐 1️⃣ CIFRAR con AES (Bash)
echo "La criptografía es muy guay :)" \
| openssl enc -aes-256-cbc -base64 -salt -pass pass:miClaveSegura

Explicación:

-aes-256-cbc → cifrado AES de 256 bits en modo CBC.

-base64 → hace que la salida sea imprimible.

-salt → añade un salt aleatorio (recomendado).

-pass pass:miClaveSegura → contraseña (puedes cambiarla).

Ejemplo de salida (cada vez será diferente):

U2FsdGVkX19uvMcLVt7B4AtpQDE/ITl3X0MuOVHxUts1t5YoRk1rD4c2+iQ=

# 🔓 2️⃣ DESCIFRAR con AES (Bash)
echo "U2FsdGVkX19uvMcLVt7B4AtpQDE/ITl3X0MuOVHxUts1t5YoRk1rD4c2+iQ=" \
| openssl enc -aes-256-cbc -d -base64 -salt -pass pass:miClaveSegura

Resultado:

La criptografía es muy guay :)


