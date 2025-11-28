🔰 Primeros pasos con Metasploit Framework (Uso Defensivo)

Metasploit es una plataforma para aprendizaje de seguridad ofensiva y pruebas de penetración éticas, siempre en sistemas que te pertenecen o tienes permiso expreso para evaluar.

Como mencionas que PostgreSQL ya está configurado, podemos avanzar con la inicialización del framework.

apt install -y metasploit-framework 
(instalar / actualizar)
systemctl enable postgresql.service

✅ 1. Iniciar Metasploit Framework

En Kali Linux, simplemente abre una terminal y ejecuta:
msfconsole


Si es la primera vez, puede tardar un poco mientras crea la base de datos.

✅ 2. Verificar conexión con la base de datos

Dentro de msfconsole, ejecuta:

db_status


Si está bien configurado, deberías ver algo similar a:

postgresql connected to msf



✅ 3. Estructura básica de Metasploit

Metasploit se basa en varios componentes:

Exploit → Código que aprovecha una vulnerabilidad.

Payload → Acción que se ejecuta tras el exploit (shell, meterpreter, etc.).

Auxiliary → Escáneres, enumeración, fuzzing, etc.

Post → Módulos para tareas posteriores a un acceso.

Encoders / Nops.

Para un uso defensivo, normalmente se trabaja con:

auxiliary/scanner → para detección de puertos, servicios y versiones.

db_nmap → integración con Nmap dentro del entorno.

✅ 4. Buscar módulos
search scanner


O busca por un servicio:

search smb

✅ 5. Ver información de un módulo
info auxiliary/scanner/portscan/tcp

✅ 6. Cargar un módulo
use auxiliary/scanner/portscan/tcp

✅ 7. Listar opciones requeridas
show options

✅ 8. Configurar parámetros
set RHOSTS 192.168.1.0/24
set THREADS 50


(Donde RHOSTS siempre debe apuntar a un entorno controlado, laboratorio propio o máquinas autorizadas.)

✅ 9. Ejecutar módulo
run

🔒 Buenas prácticas para un uso seguro y ético

Usa Metasploit solo en laboratorios locales (ej.: máquinas virtuales).

No ejecutes exploits contra redes o sistemas sin permiso.

Mantén un registro de pruebas (logs).

Actualiza Metasploit:

sudo msfupdate

🧪 Recomendación: Crear un laboratorio seguro

Un entorno ideal de práctica:

Kali Linux (atacante)

Metasploitable2 o Metasploitable3 (máquina vulnerable)

OWASP Broken Web Apps

Windows 10/11 con vulnerabilidades intencionales (FlareVM o CommandoVM)

Todo en una red virtual cerrada (VirtualBox, VMware, Proxmox).