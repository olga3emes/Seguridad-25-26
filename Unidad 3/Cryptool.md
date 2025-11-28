# Cryptool
https://www.cryptool.org/en/cto/  
https://cryptool.org/ct1download/SetupCrypTool_1_4_42_es.exe 
## 1.	Descarga e instala la aplicación Cryptool. 
Navega por los menús de la aplicación y localiza los siguientes algoritmos criptográficos: 
•	Vigenère
•	Rijndael
•	MD5
•	SHA-256
•	RIPEMD-160
•	Localiza también la conversión a/de base64.
## 2.	Encripta con el algoritmo de Vigenère el texto “Vigenere es un metodo clasico”  usando la clave “CLASICO”.
Y verifica que el hash RIPEMD-160 del resultado es 
81 A5 7A 0E F2 89 8E 32 AB A0 36 4A 7A 51 49 73 F1 37 9A 10

![alt text](image.png)
 
a.	Desencripta el texto 
Nrempbdiqxw. Yh cwalitniagx.
mediante el algoritmo de Vigenère usando la clave “INTENTA”.
b.	Encripta con Cryptool el texto “Interoperabilidad” usando el algoritmo Triple-DES en modo Electronic Codebook (ECB) con la clave hexadecimal 30493080103012853712DF812AFE81AA   .
Verifica con Cryptool que el hash RIPEMD-160 del resultado es 
DD00D19DD412EC76A165951D0152530D6776828A
 
## 3.	Desencripta con Cryptool, usando el algoritmo Triple-DES ECB y la clave 
0102AFCDBE17BEA171BE91F9A8EFA183
el texto
Yz4UUTujMNU7zZ9SyrpzqeMUGcFMAyzQzc1H2sry7gj6SP7ZnBnh8Q==
El texto está codificado en base64 (hay que decodificarlo previamente).
## 4.	Comprobemos los problemas de las funciones hash md5 y sha1 (según HashZoo [hashzoo] se consideran “rotas”). Existen técnicas [rainbow_tables] para intentar obtener el texto original a partir de un hash del mismo. Existen también bases de datos con los hashes de gran cantidad de palabras.
## 5.	Mediante los siguientes datos, averigua el mensaje original y los datos de la codificación:
a)	El siguiente texto está codificado en base64
RVNPUyBFRFQgR1QgREUgUFJBQVBDSUFJRVJG
b)	El nombre del algoritmo de codificación y la clave usados para encriptar el texto del punto a) está oculto mediante Vigenere en este texto:
Tazc Jxrte eoe b=5
El hash  en sha1 de la clave de Vigenere es
f49727fb0deaca2d815eeee5c9f56d4a81970ed8


