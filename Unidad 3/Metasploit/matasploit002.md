🧩 Estructura de Metasploit Framework

Metasploit se organiza como una colección de módulos, cada uno con un propósito dentro del flujo de un test de seguridad ético.

sudo msfdb      

Manage the metasploit framework database

You can use an specific port number for the
PostgreSQL connection setting the PGPORT variable
in the current shell.

Example: PGPORT=5433 msfdb init

  msfdb init     # start and initialize the database
  msfdb reinit   # delete and reinitialize the database
  msfdb delete   # delete database and stop using it
  msfdb start    # start the database
  msfdb stop     # stop the database
  msfdb status   # check service status
  msfdb run      # start the database and run msfconsole



Los módulos principales son:

1️⃣ Auxiliary Modules (auxiliary)

Función: apoyo, recolección de información, análisis y pruebas no explotadoras.

Incluyen:

Escáneres de puertos → auxiliary/scanner/portscan/...

Escáneres de servicios → SMB, FTP, HTTP, etc.

Fuerza bruta (solo donde está permitido)

Análisis de vulnerabilidades

Fuzzers

Sniffers

📌 Son los módulos más seguros para comenzar, porque no explotan vulnerabilidades.

2️⃣ Exploit Modules (exploit)

Función: ejecutar vulnerabilidades conocidas contra sistemas autorizados.

Se clasifican por tecnologías o servicios:

exploit/windows/smb/...

exploit/linux/http/...

exploit/multi/ssh/...

Cada exploit requiere:

Un target (tipo de sistema)

Un payload (qué hacer después)

📌 Solo para entornos controlados.

3️⃣ Payloads (payload)

Son las acciones que se ejecutan después del exploit.

Tipos:

➤ Singles

Un payload autónomo.
Ej: abrir un shell básico.

➤ Stagers

Payload pequeño que “prepara” una conexión para recibir otro payload más grande.

➤ Stages

El payload más grande que se carga luego. Ejemplo típico:

Stager: prepara la conexión

Stage: carga Meterpreter

📌 Se eligen después de iniciar un módulo de exploit.

4️⃣ Post Modules (post)

Acciones que se pueden realizar después de obtener acceso (solo en laboratorios):

Enumeración del sistema

Recolección de información

Escalado de privilegios (en sandbox autorizada)

Persistencia (solo educativo)

Ejemplo de estructura:

post/windows/gather/enum_logged_on_users

5️⃣ Encoders (encoder)

Ofuscadores de payload que:

Evitan detecciones simples

Cambian la firma del payload

Ejemplo:

encoder/x86/shikata_ga_nai


📌 Hoy en día tienen poca utilidad práctica debido a EDR modernos.

6️⃣ NOPs (nops)

Instrucciones que “rellenan” buffers en arquitecturas antiguas.
Ejemplo común: nops/x86/single_byte

Principalmente se usan en exploits muy manuales o antiguos.

7️⃣ Estructura de archivos del proyecto

En Kali, Metasploit está ubicado en:

/usr/share/metasploit-framework/


Dentro encontrarás:

modules/ → todos los módulos (auxiliary, exploit, payload, post)

tools/ → herramientas CLI

lib/msf/ → librerías internas Ruby

plugins/ → plugins adicionales

8️⃣ Jerarquía completa de módulos

Aquí tienes una vista resumen:

modules/
├── auxiliary/
│   ├── admin/
│   ├── scanner/
│   ├── dos/
│   └── ...
├── exploits/
│   ├── windows/
│   ├── linux/
│   ├── multi/
│   └── ...
├── payloads/
│   ├── singles/
│   ├── stagers/
│   └── stages/
├── post/
│   ├── linux/
│   ├── windows/
│   └── ...
├── encoders/
└── nops/

9️⃣ Estructura en el uso práctico (msfconsole)

Dentro de Metasploit, la lógica es:

👉 1. Elegir un módulo
search http
use auxiliary/scanner/http/http_version

👉 2. Ver parámetros
show options

👉 3. Configurar
set RHOSTS 192.168.1.10

👉 4. Ejecutar
run


Este flujo funciona igual para auxiliary, exploits, post, etc.

🔟 Workflow completo en un test ético

Un pentest con Metasploit suele seguir:

Reconocimiento → (auxiliary, scanners)

Enumeración detallada → módulos específicos

Correlación de vulnerabilidades (db_nmap + búsqueda de exploits)

Prueba controlada de explotación (solo con permiso)

Post-explotación en laboratorio

Reporte