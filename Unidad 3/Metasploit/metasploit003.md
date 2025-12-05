# Fase de reconocimiento pasivo
Dentro de la fase de reconocimiento pasivo, no es raro oír hablar de OSINT (Open Source INTelligence), un concepto que significa: «cualquier información obtenida a través de datos públicos». De hecho, el objetivo es obtener la máxima información posible sobre el perímetro objetivo, sin tener que hacer una conexión directa.

1. Información de la empresa
Tan pronto como se quiera atacar a una empresa, puede ser interesante obtener información sobre esta (dirección, fecha de creación, hechos significativos, etc.), así como sobre los empleados en riesgo (gerentes, asistentes ejecutivos, empleados descontentos, etc.). El módulo auxiliary/gather/corpwatch_lookup_name permite obtener información sobre una empresa gracias a su nombre. La obtención de esta información se realiza de forma totalmente pasiva, consultando un informe anual (Formulario 10-K) requerido por la SEC (Securities and Exchange Commission), que incluye la información de cada empresa estadounidense que cotiza en bolsa:

msf6 > use auxiliary/gather/corpwatch_lookup_name   
msf6 auxiliary(gather/corpwatch_lookup_name) > show options   
  
Module options (auxiliary/gather/corpwatch_lookup_name):   
  
  Name              Current Setting  Required  Description  
  ----              ---------------  --------  -----------  
  COMPANY_NAME                       yes       Search for companies  
  CORPWATCH_APIKEY                   no        Use this API key  
  LIMIT             5                yes       Limit the results 
  YEAR              2018             no        Year to look up  
  
msf6 auxiliary(gather/corpwatch_lookup_name) > set COMPANY_NAME Microsoft 
COMPANY_NAME => Microsoft   
  
msf6 auxiliary(gather/corpwatch_lookup_name) > exploit   
  
[*] Company Information  
---------------------------------  
[*] CorpWatch (cw) ID): cw_4803  
[*] Company Name: MICROSOFT CORP  
[*] Address: ONE MICROSOFT WAY, REDMOND WA 98052-6399  
[*] Sector: Business services  
[*] Industry: Services-prepackaged software  
[*] Company Information 
2. Consulta de servidores DNS
Solo utilizando el nombre de dominio ya es posible recopilar mucha información, especialmente sobre los registros DNS, en parte gracias al módulo auxiliary/gather/enum_dns:

msf6 > use auxiliary/gather/enum_dns  
msf6 auxiliary(gather/enum_dns) > show options  
Module options (auxiliary/gather/enum_dns):   
  Name         Current Setting  Required  Description  
  ----         ---------------  --------  -----------  
  DOMAIN                        yes       The target domain  
  ENUM_A       true             yes       Enumerate DNS A record 
  ENUM_AXFR    true             yes       Initiate a zone transfer 
  ENUM_BRT     false            yes       Brute force subdomains 
  ENUM_CNAME   true             yes       Enumerate DNS CNAME record  
  ENUM_MX      true             yes       Enumerate DNS MX record 
  ENUM_NS      true             yes       Enumerate DNS NS record 
  ENUM_RVL     false            yes       Reverse lookup  
  ENUM_SOA     true             yes       Enumerate DNS SOA record 
  ENUM_SRV     true             yes       Enumerate the SRV records  
  ENUM_TLD     false            yes       Perform a TLD expansion 
  ENUM_TXT     true             yes       Enumerate DNS TXT record 
  IPRANGE                       no        The target  
  NS                            no        Specify the nameserve 
  STOP_WLDCRD  false            yes       Stops bruteforce  
  THREADS                       no        Threads for ENUM_BRT  
  WORDLIST                      no        Wordlist of subdomains  
  
msf6 auxiliary(gather/enum_dns) > set DOMAIN microsoft.com   
DOMAIN => microsoft.com  
  
 msf6 auxiliary(gather/enum_dns) > exploit  
[*] querying DNS NS records for microsoft.com  
[+] microsoft.com NS: ns3.msft.net.  
[+] microsoft.com NS: ns4.msft.net.  
[+] microsoft.com NS: ns1.msft.net.  
[+] microsoft.com NS: ns2.msft.net.  
[*] Attempting DNS AXFR for microsoft.com from ns3.msft.net.  
[...] 
Este módulo también se puede utilizar en modo activo gracias a la activación de ENUM_BRT, que permite realizar ataques por fuerza bruta (basados en el diccionario definido en WORDLIST) en los subdominios.

3. Los subdominios
Como se mencionó en la introducción, los administradores de red y de sistemas deben cubrir la seguridad de todo su perímetro, mientras que un atacante solo necesita una vulnerabilidad. De hecho, el sitio «escaparate» generalmente es donde se concentran los esfuerzos de seguridad. Sin embargo, los subdominios a menudo no disponen de las mismas restricciones de seguridad, lo que puede permitir que un atacante consiga un primer acceso.

El módulo gather/searchengine_subdomains_collector permite extraer los subdominios usando diferentes buscadores, como Google, Bing o incluso Yahoo!:

msf6 > use auxiliary/gather/searchengine_subdomains_collector  
msf6 auxiliary(gather/searchengine_subdomains_collector) > show options 
  
Module options (auxiliary/gather/searchengine_subdomains_collector):  
  
  Name        Current Setting  Required  Description  
  ----        ---------------  --------  -----------  
  ENUM_BING   true             yes       Enable Bing Search  
  ENUM_YAHOO  true             yes       Enable Yahoo Search  
  IP_SEARCH   true             no        Enable ip of subdomains  
  TARGET                       yes       The target to locate  
  
 msf6 auxiliary(gather/searchengine_subdomains_collector) > set  
TARGET rapid7.com  
TARGET => rapid7.com  
  
 msf6 auxiliary(gather/searchengine_subdomains_collector) > exploit 
  
[*] Searching Bing for subdomains from domain:rapid7.com   
[*] Searching Yahoo for subdomains from domain:rapid7.com  
[+] domain:rapid7.com subdomain: www.rapid7.com   
[*] Searching Bing for subdomains from ip:13.249.2.67   
[*] Searching Yahoo for subdomains from ip:13.249.2.67  
[+] domain:rapid7.com subdomain: insight.rapid7.com   
[*] Searching Bing for subdomains from ip:35.158.18.176   
[*] Searching Yahoo for subdomains from ip:35.158.18.176  
[+] ip:35.158.18.176 subdomain: insight.rapid7.com  
[*] Searching Bing for subdomains from ip:52.57.114.115   
[*] Searching Yahoo for subdomains from ip:52.57.114.115  
[+] domain:rapid7.com subdomain: exposure-analytics.insight.rapid7.com 
[*] Searching Bing for subdomains from ip:52.45.109.205   
[*] Searching Yahoo for subdomains from ip:52.45.109.205  
[+] ip:52.45.109.205 subdomain: exposure-analytics.insight.rapid7.com 
[*] Searching Bing for subdomains from ip:34.206.30.59   
[*] Searching Yahoo for subdomains from ip:34.206.30.59   
[*] Auxiliary module execution completed 
Como resultado, la superficie de ataque se amplía considerablemente, lo que representa un activo importante para los atacantes.

4. Las direcciones de correo electrónico
En escenarios de tipo red team o campañas de phishing, la obtención de direcciones de correo electrónico es la piedra angular de un ataque exitoso. Para ello, el Framework Metasploit integra el módulo auxiliary/gather/search_email_collector, que permite buscar las direcciones de correo electrónico de un dominio en particular a través de búsquedas en diferentes motores, como Google, Bing o Yahoo!. Estas técnicas de recuperación de direcciones de correo electrónico son similares a las de Google hacking:

msf6 > use auxiliary/gather/search_email_collector  
msf6 auxiliary(gather/search_email_collector) > show options  
  
Module options (auxiliary/gather/search_email_collector):   
  
  Name           Current Setting  Required  Description  
  ----           ---------------  --------  -----------  
  DOMAIN                          yes       The domain name to  
locate email addresses for  
  OUTFILE                         no        A filename to store 
the generated email list  
  SEARCH_BING    true             yes       Enable Bing as a  
backend search engine  
  SEARCH_GOOGLE  true             yes       Enable Google as a  
backend search engine  
  SEARCH_YAHOO  true              yes       Enable Yahoo! as a  
backend search engine  
  
msf6 auxiliary(gather/search_email_collector) > set DOMAIN  
microsoft.com  
DOMAIN => microsoft.com  
msf6 auxiliary(gather/search_email_collector) > exploit  
  
[*] Harvesting emails .....   
[*] Searching Google for email addresses from microsoft.com  
[*] Extracting emails from Google search results...   
[*] Searching Bing email addresses from microsoft.com   
[*] Extracting emails from Bing search results...   
[*] Searching Yahoo for email addresses from microsoft.com  
[*] Extracting emails from Yahoo search results...   
[*] Located 3 email addresses for microsoft.com  
[*]     actvar@microsoft.com  
[*]     billing@microsoft.com  
[*]     prcfd@microsoft.com  
[*] Auxiliary module execution completed 
5. Shodan y Censys
Shodan y Censys tienen ciertas funcionalidades similares. De hecho, se trata de dos motores de búsqueda para analizar todas las direcciones IP expuestas en Internet.

A diferencia de Google, Bing o Yahoo!, cuyo objetivo es recuperar los datos contenidos dentro de las páginas, el objetivo de estos dos nuevos motores de búsqueda es consultar los diferentes puertos y recuperar los datos presentes dentro de los banners, gracias al uso de herramientas de escaneo de puertos como ZMap (en el marco de Censys).

Aquí, la pasividad de este reconocimiento radica en el hecho de que los datos utilizados se obtienen directamente por estos motores de búsqueda.

El Framework Metasploit integra tres módulos que resultan especialmente interesantes para este fin:

auxiliary/gather/shodan_search

El auxiliar auxiliary/gather/shodan_search permite interactuar con la API Shodan. El único requisito previo necesario para poder usar este módulo es crear una cuenta gratuita en el sitio https://www.shodan.io/

msf6 > use auxiliary/gather/shodan_search  
msf6 auxiliary(gather/shodan_search) > show options  
  
Module options (auxiliary/gather/shodan_search):  
  
  Name           Current Required  Description  
  ----           ------- --------  -----------  
  DATABASE       false   no        Add search to the database  
  MAXPAGE        1       yes       Max amount of pages to collect 
  OUTFILE                no        Keywords you want to search for 
  REGEX          .*      yes       Regex search for a specific  
IP/City/Country/Hostname  
  SHODAN_APIKEY          yes       The SHODAN API key 

  
El uso del módulo de búsqueda Shodan facilita la explotación masiva. De hecho, en el caso de que un atacante descubra una vulnerabilidad en la versión 2.2.3 de Apache, Shodan le permitiría deshacerse rápidamente de todas las máquinas vulnerables en Internet:

msf6 auxiliary(gather/shodan_search) > set DATABASE true  
DATABASE => true  
  
msf6 auxiliary(gather/shodan_search) > set QUERY Apache 2.2.3   
QUERY => Apache 2.2.3  
  
msf6 auxiliary(gather/shodan_search) > set SHODAN_APIKEY  
8bRLz4vnrSE.........ZPMH08Rn  
SHODAN_APIKEY => 8bRLz4vnrSE.........ZPMH08Rn  
  
msf6 auxiliary(gather/shodan_search) > exploit   
  
[*] Total: 170852 on 1709 pages. Showing: 1 page(s)  
[*] Collecting data, please wait...  
  
Search Results  
==============  
  
IP:Port               City                Country  
-------               ----                -------  
103.219.124.239:80    N/A                 Korea  
103.241.130.167:80    N/A                 Japan  
103.249.113.180:81    N/A                 Japan  
103.251.77.42:80      N/A                 Japan  
107.6.6.53:80         N/A                 Canada  
110.50.245.16:80      N/A                 Japan  
114.203.85.229:80     Bucheon             Korea  
118.25.238.254:55443  N/A                 China  
119.18.220.172:80     Tokyo               Japan  
119.82.24.188:80      Chiyoda-ku          Japan  
128.252.124.56:80     Saint Louis         United States  
140.112.14.12:80      Taipei              Taiwan  
143.191.207.67:80     Tokyo               Japan  
147.32.155.254:80     Prague              Czech Republic  
153.122.28.138:9443   N/A                 Japan  
162.218.228.43:80     N/A                 United States  
166.78.135.78:80      San Antonio         United States  
177.36.30.222:80      N/A                 Brazil 
La activación de las inserciones en la base de datos (set DATABASE true) se comprueba con los comandos que se muestran más atrás:

msf6 > hosts -c address,info,comments   
  
Hosts   
=====   
  
address          info               comments  
-------          ----               --------  
34.232.157.117  (CentOS)            Added from Shodan  
37.220.1.30                         Added from Shodan  
45.231.253.6    (CentOS)            Added from Shodan  
46.235.43.3     (CentOS)            Added from Shodan  
49.212.6.109    (CentOS)            Added from Shodan  
49.212.7.114    (CentOS)            Added from Shodan  
50.23.90.237    (CentOS)            Added from Shodan  
50.57.195.17    (Red Hat)           Added from Shodan  
50.116.13.107   (CentOS)            Added from Shodan  
50.198.214.168  (CentOS)            Added from Shodan 


auxiliary/gather/shodan_honeyscore

Como parte de las pruebas de intrusión en un perímetro amplio, es posible que el equipo defensivo haya implementado medios de protección, como los honeypots.

Un honeypot es un método que consiste en atraer potenciales atacantes a máquinas diseñadas para este fin con objeto de identificar las técnicas de explotación para neutralizarlas en el futuro sobre otras máquinas del perímetro.

El auxiliar auxiliary/gather/shodan_honeyscore le permite obtener una puntuación entre 0.0 y 1.0 gracias al motor de búsqueda Shodan, determinando la posibilidad de que la máquina de destino sea un honeypot:

msf6 > use auxiliary/gather/shodan_honeyscore  
msf6 auxiliary(gather/shodan_honeyscore) > show options  
  
Module options (auxiliary/gather/shodan_honeyscore):  
  
Name           Current Setting  Required  Description  
----           ---------------  --------  -----------  
SHODAN_APIKEY                   yes       The SHODAN API key  
TARGET                          yes       The target to get the score 
  
msf6 auxiliary(gather/shodan_honeyscore) > set SHODAN_APIKEY  
8bRLz4vnrSE.........ZPMH08Rn  
SHODAN_APIKEY => 8bRLz4vnrSE.........ZPMH08Rn  
  
msf6 auxiliary(gather/shodan_honeyscore) > set TARGET 34.232.157.117  
TARGET => 34.232.157.117  
  
msf6 auxiliary(gather/shodan_honeyscore) > exploit   
  
[*] Scanning 34.232.157.117  
[-] 34.232.157.117 is not a honeypot   
[*] 34.232.157.117 honeyscore: 0.0/1.0  
[*] Auxiliary module execution completed 
auxiliary/gather/censys_search

De la misma manera que antes, el módulo auxiliary/gather/censys_search permite interactuar con la API de Censys. Al igual que sucede con el módulo Shodan, el único requisito previo necesario para usar este módulo es crear una cuenta gratuita en el sitio https://censys.io

msf6 > use auxiliary/gather/censys_search   
msf6 auxiliary(gather/censys_search) > show options   
  
Module options (auxiliary/gather/censys_search):   
  
Name               Current Setting  Required  Description  
----               ---------------  --------  -----------  
CENSYS_DORK                         yes       The Censys Search 
Dork  
CENSYS_SEARCHTYPE certificates      yes       The Censys Search 
Type (Accepted: certificates, ipv4, websites)  
CENSYS_SECRET                       yes       The Censys API SECRET  
CENSYS_UID                          yes       The Censys API UID  
  
msf6 auxiliary(gather/censys_search) > set CENSYS_DORK rapid7  
CENSYS_DORK => rapid7  
  
msf6 auxiliary(gather/censys_search) > set CENSYS_SECRET  
k07OQqg66O9i...........XlPPVY38  
CENSYS_SECRET => k07OQqg66O9i...........XlPPVY38  
  
msf6 auxiliary(gather/censys_search) > set CENSYS_UID  
28704575-...-f594b53ec0  
  
CENSYS_UID => 28704575-...-f594b53ec0  
  
msf6 auxiliary(gather/censys_search) > exploit  
  
[+] 199.15.214.152 - C=US, ST=TX, L=Austin, O=Rapid7,  
CN=MetasploitSelfSignedCA    
[+] 31.214.157.19 - C=US, ST=TX, L=Austin, O=Rapid7,  
CN=MetasploitSelfSignedCA   
[+] 31.220.7.39 - C=US, ST=TX, L=Austin, O=Rapid7,  
CN=MetasploitSelfSignedCA   
[+] 168.253.216.190 - C=US, ST=TX, L=Austin, O=Rapid7,  
CN=MetasploitSelfSignedCA   
[+] 52.88.1.225 - C=US, ST=TX, L=Austin, O=Rapid7,  
CN=MetasploitSelfSignedCA   
[+] 208.118.237.41 - C=US, ST=TX, L=Austin, O=Rapid7,  
CN=MetasploitSelfSignedCA   
[+] 64.125.235.5 - C=US, ST=TX, L=Austin, O=Rapid7,  
CN=MetasploitSelfSignedCA   
[+] 208.118.237.39 - C=US, ST=TX, L=Austin, O=Rapid7,  
CN=MetasploitSelfSignedCA   
[+] 208.118.237.40 - C=US, ST=TX, L=Austin, O=Rapid7,  
CN=MetasploitSelfSignedCA   
[+] 208.118.227.12 - C=US, ST=TX, L=Austin, O=Rapid7,  
CN=MetasploitSelfSignedCA   
[+] 208.118.237.38 - C=US, ST=TX, L=Austin, O=Rapid7,  
CN=MetasploitSelfSignedCA 