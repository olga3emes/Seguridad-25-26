# Glosario de Ataques y vulnerabilidades 
- **Por vector/técnica**: Malware, red, aplicación, identidad/acceso, ingeniería social, físico, hardware/firmware, ICS/OT, nube, mobile/IoT, cripto/criptografía, etc.
- **Por objetivo/intento**: Confidencialidad (robar datos), Integridad (modificar/falsear), Disponibilidad (causar caída), Reconocimiento, Persistencia/escalada, Exfiltración, Destrucción.
- **Por actor posible**: Cibercriminales, APT/estado, hacktivistas, insiders, script kiddies.
________________________________________
### 1. Malware (software malicioso)
- **Virus**: Programa que se autoinyecta en archivos y se propaga al ejecutarlos.
- **Worm (gusano)**: Se auto-replica a través de redes sin necesidad de un huésped.
- **Troyano (Trojan)**: Software que aparenta ser legítimo pero contiene funcionalidad maliciosa.
- **Ransomware**: Cifra archivos o bloquea sistemas y exige rescate para recuperarlos.
- **Rootkit**: Herramientas para ocultar presencia y mantener acceso privilegiado.
- **Spyware**: Recopila información del usuario (teclas, hábitos, archivos) sin permiso.
- **Adware**: Muestra publicidad no deseada; a veces con capacidades espía.
- **Botnet / Bots**: Conjunto de máquinas comprometidas controladas remotamente.
- **Fileless malware**: Opera en memoria o usando herramientas legítimas (p.ej. PowerShell) sin dejar archivos en disco.
- **RAT (Remote Access Trojan)**: Permite control remoto completo del equipo víctima.
- **Keylogger**: Registra pulsaciones de teclado para robar credenciales.
- **Backdoor**: Puerta trasera para acceso persistente al sistema.
- **Dropper/Downloader**: Pequeño binario que descarga/instala otro malware.
- **Bootkit**: Malware que infecta el sector de arranque o UEFI para persistir antes del SO.
- **Firmware/root malware**: Infecta BIOS/UEFI o firmware de dispositivos para persistencia profunda.
### 2. Ataques a redes y capa de transporte
- **DDoS (Distributed Denial of Service)**: Sobrecargar recursos con tráfico distribuido para causar indisponibilidad.
- **SYN flood**: Consumo de conexiones TCP incompletas para agotar recursos.
- **Amplification/Reflection (NTP/DNS/SSD P)**: Usar servidores públicos para amplificar tráfico hacia la víctima.
- **Smurf / Fraggle**: Uso de broadcast ICMP/UDP para amplificar tráfico.
- **ARP spoofing / ARP poisoning**: Engañar la tabla ARP para interceptar tráfico local.
- **IP spoofing**: Falsificar dirección IP de origen para evadir filtros o redirigir respuestas.
- **Packet sniffing / eavesdropping**: Captura de tráfico en la red para leer datos sensibles.
- **Man-in-the-Middle (MITM)**: Interceptación y posible modificación de comunicaciones.
- **Session hijacking**: Secuestrar una sesión (cookies, tokens) para acceder como usuario.
- **TCP/IP fragmentation attacks (teardrop)**: Envío de paquetes fragmentados malformados para causar fallas.
- **Port scanning (reconocimiento)**: Exploración de puertos para descubrir servicios vulnerables.
- **DNS poisoning / cache poisoning**: Corromper resolución DNS para redirigir tráfico a sitios maliciosos.
- **DNS hijacking / DNS sinkholing**: Secuestrar o controlar resolución DNS a nivel ISP/dispositivo.
- **BGP hijacking**: Secuestrar rutas de Internet para redirigir tráfico globalmente.
### 3. Ataques a aplicaciones web
- **SQL Injection (SQLi)**: Inyección de comandos SQL para leer/modificar BD.
- **Blind SQLi**: Variante sin respuesta directa; explotación por inferencia.
- **Cross Site Scripting (XSS)**: Inyección de scripts en páginas para ejecutar código en navegadores de otros usuarios.
- **Stored XSS / Reflected XSS / DOM XSS**: Variantes según dónde se almacene/ejecute el payload.
- **Cross Site Request Forgery (CSRF)**: Inducir a un usuario autenticado a realizar acciones no deseadas.
- **Remote Code Execution (RCE)**: Ejecutar código arbitrario en el servidor a través de vulnerabilidad.
- **Local File Inclusion (LFI) / Remote File Inclusion (RFI)**: Inclusión de archivos locales/remotos para ejecutar código.
- **Path Traversal**: Acceso a archivos fuera del directorio previsto mediante “..” u otras técnicas.
- **Insecure deserialization**: Ejecución de código por deserializar datos maliciosos.
- **Server-Side Request Forgery (SSRF)**: Forzar al servidor a hacer peticiones internas/externas arbitrarias.
- **XML External Entity (XXE)**: Abusar de parsers XML para leer archivos locales o realizar SSRF.
- **Clickjacking**: Engañar interfaz para que el usuario haga clic en elementos invisibles.
- **Broken authentication / Broken access control**: Fallos que permiten escalada de privilegios o acceso no autorizado.
- **Open redirect**: Redirecciones controladas por atacante para phishing.
### 4. Ataques a identidad y control de acceso
- **Phishing**: Engaño por correo/web para robar credenciales o datos.
- **Spear phishing**: Phishing dirigido y personalizado contra objetivos concretos.
- **Whaling**: Phishing dirigido a altos ejecutivos (CEO, CFO).
- **Vishing**: Phishing por voz/llamadas telefónicas.
- **Smishing**: Phishing por SMS.
- **Credential stuffing**: Uso de credenciales filtradas en múltiples servicios.
- **Password spraying**: Intentos con pocas contraseñas comunes contra muchas cuentas.
- **Brute force**: Prueba masiva de contraseñas hasta acertar.
- **Password cracking (rainbow tables, dictionary)**: Descifrar hashes de contraseñas por precomputación o fuerza.
- **Session fixation**: Forzar una sesión conocida a un usuario para secuestrarla.
- **SIM swapping**: Secuestrar número telefónico para pasar autenticación por SMS/2FA.
- **Account takeover (ATO)**: Tomar control total de una cuenta.
- **Business Email Compromise (BEC) / CEO fraud**: Engañar para autorizar transferencias/fraudes financieros.
- **Privilege escalation (vertical / horizontal)**: Obtener mayores permisos en un sistema.
### 5. Ingeniería social (no técnico directo)
- **Pretexting**: Inventar una excusa para obtener información (p.ej. hacerse pasar por soporte).
- **Baiting**: Ofrecer algo (USB, descarga) para que la víctima lo tome e infecte su sistema.
- **Quid pro quo**: Ofrecer servicio a cambio de información/acciones.
- **Tailgating / piggybacking**: Acceder físicamente a una zona segura siguiendo a alguien autorizado.
- **Impersonation**: Suplantación directa de identidad de una persona/entidad.
### 6. Amenazas internas (Insider threats)
- **Malicious insider**: Empleado que intencionalmente roba o sabotea.
- **Negligent insider**: Errores humanos que provocan fugas o brechas.
- **Compromised insider**: Cuenta legítima tomada por atacantes y usada desde dentro.
### 7. Amenazas a la cadena de suministro (Supply chain)
- **Software supply chain attacks**: Comprometer una librería, paquete o actualización legítima (p.ej. atacar repositorios) para distribuir malware.
- **Hardware supply chain**: Inserción de componentes maliciosos o manipulados en hardware.
- **Third party service compromise**: Atacar proveedores para pivotar a clientes.
### 8. Amenazas específicas a la nube y virtualización
- **Misconfiguration (config errors)**: Exposición de buckets S3, reglas de firewall abiertas, etc.
- **Privilege escalation en nube**: Uso de permisos mal configurados para obtener más privilegios.
- **Credential leakage (keys/secret)**: Fugas de claves API o secretos que permiten acceso.
- **VM escape**: Escapar de una máquina virtual para afectar al hypervisor o VMs vecinas.
- **Side channel en nube**: Extraer información por canales laterales en infraestructuras compartidas.
- **Abuse of cloud services (cryptojacking en cloud)**: Usar recursos cloud para minar criptomonedas.
### 9. Ataques contra dispositivos móviles y IoT
- **Mobile malware (Android/iOS trojans)**: Apps maliciosas que roban datos o controlan el dispositivo.
- **App sideloading abuse**: Instalación de apps fuera de tiendas oficiales que contienen malware.
- **Malicious QR codes**: QR que dirigen a descargas/malware o phishing.
- **Bluetooth attacks (BlueBorne, Bluejacking)**: Exploits que atacan servicios Bluetooth.
- **NFC/RFID skimming**: Leer tarjetas/contactless para clonar datos.
- **IoT default credentials / bulldozed devices**: Dispositivos con credenciales por defecto fáciles de comprometer.
- **Botnets IoT (p.ej. Mirai)**: Recolección de dispositivos IoT para DDoS u otras actividades.
### 10. Ataques dirigidos y persistentes
- **APT (Advanced Persistent Threat)**: Operaciones largas y sofisticadas, a menudo por actores estatales, para espionaje/exfiltración.
- **Watering hole**: Comprometer sitios frecuentados por la víctima para infectarlos.
- **Living off the land (LotL)**: Uso de herramientas legítimas del sistema para evitar detección.
- **Lateral movement**: Movimiento dentro de la red para acceder a activos objetivos.
- **Reconnaissance (scanning, OSINT)**: Recolección de información previa al ataque.
### 11. Ataques a sistemas industriales / ICS / SCADA
- **ICS/SCADA attacks (e.g., Stuxnet-like)**: Malware o comandos que interfieren con controladores industriales.
- **Protocol abuse (Modbus, DNP3)**: Uso/alteración de protocolos industriales inseguros.
- **Physical sabotage**: Manipulación física de equipos industriales para causar daño.
### 12. Amenazas físicas y al hardware
- **Robo de dispositivos**: Robo de laptops/USB para obtener datos.
- **Eavesdropping físico (shoulder surfing)**: Observación directa de credenciales o pantalla.
- **Hardware tampering**: Modificar hardware para insertar puertas traseras.
- **Evil maid attack**: Acceso físico temporal a una máquina para infectarla (p.ej. hotel).
### 13. Ataques cripto y contra la confidencialidad de datos
- **Brute force sobre criptografía**: Forzar claves por potencia bruta (viable solo para claves débiles).
- **Dictionary / rainbow table attacks**: Atacar hashes de contraseñas con tablas precomputadas.
- **Birthday attack**: Ataque para colisiones criptográficas (p.ej. en hashes).
- **Replay attack**: Repetir comunicaciones válidas para reproducir acciones.
- **Downgrade attack**: Forzar uso de protocolos/cifras menos seguras para facilitar el ataque.
- **Side channel attacks (timing, power, electromagnetic)**: Extraer claves por medición de fugas físicas.
### 14. Fraude, manipulación y engaños
- **Business Email Compromise (BEC)**: Engaños por correo para transferir dinero o información sensible.
- **Invoice fraud / CEO fraud**: Suplantación para cobrar facturas falsas.
- **Fake apps / counterfeit services**: Creación de apps o servicios falsos para robar.
### 15. Explotación de vulnerabilidades y zero day
- **Zero day exploit**: Vulnerabilidad desconocida por el fabricante explotada antes de parche.
- **Exploit chaining**: Combinación de varias vulnerabilidades para lograr objetivos más amplios.
- **Proof of concept (PoC) disclosure abuse**: Uso malintencionado de PoC público para ataques masivos.
### 16. Ataques contra la disponibilidad y destrucción
- **Wiper malware**: Borrado o daño de datos para destruir información.
- **Ransom denial-of-service (RDOS)**: Combinar extorsión con ataques de denegación.
- **Operational sabotage**: Manipulación de procesos para interrumpir operaciones.
### 17. Técnicas de ocultación y evasión
- **Polymorphic / Metamorphic malware**: Cambian su código para evadir firmas.
- **Anti VM / anti debugging**: Detectan entornos de análisis para evitar ser estudiados.
- **Encubrimiento en tráfico cifrado (TLS misuse)**: Ocultar comunicaciones maliciosas dentro de TLS.
### 18. Malware financiero y robo monetario
- **Banking trojans**: Diseñados para robar credenciales bancarias y realizar fraude.
- **Card skimming / ATM skimmer**: Capturar datos de tarjetas en terminales y cajeros.
- **Cryptojacking**: Usar recursos de terceros para minar criptomonedas sin permiso.
### 19. Amenazas legales/operacionales y cumplimiento
- **Data leakage / accidental exposure**: Exposición accidental de datos sensibles (p.ej. S3 público).
- **Privacy violations**: Recolección/compartición de datos personales sin consentimiento.
- **Regulatory non compliance attacks**: Aprovechar incumplimientos para multas, reputación y explotación.
### 20. Técnicas de reconocimiento y post-explotación
- **Credential harvesting (harvesting de tokens)**: Recolección masiva de credenciales/tokens.
- **Pass-the-Hash / Pass-the-Ticket**: Uso de hashes o tickets de autenticación para moverse lateralmente.
- **Kerberoasting / AS-REP roasting**: Ataques a protocolos de autenticación Windows (Kerberos).
- **Golden Ticket / Silver Ticket**: Crear tickets Kerberos falsos para acceso persistente.
### 21. Amenazas emergentes / híbridas
- **Deepfake / synthetic identity attacks**: Uso de IA para crear audio/video falsos para fraude o extorsión.
- **AI assisted attacks**: Automatización y mejora de phishing, ingeniería social o descubrimiento de vulnerabilidades mediante IA.
- **Quantum threats (futuras)**: Eventual quebrantamiento de cifrados clásicos por computación cuántica (a futuro).

