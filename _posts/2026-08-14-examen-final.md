---
layout: post
title: "EXAMEN FINAL - Capstone Threat-Informed"
date: 2026-08-14 18:00:00 -0500
permalink: /posts/examen-final/
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
| Dominios comprometidos | Dominio | TLP:AMBER / TLP:CLEAR | Táctico |
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

# ACTO 2 — PLAN DE ATAQUE Y RECONOCIMIENTO DE RED

## 1. Configuración de red de Kali

Se verificó la configuración de red del equipo atacante Kali Linux, identificándose la dirección IP `10.0.2.10/24` asignada a la interfaz `eth0`.

![Captura de Red kali](/assets/images/Examenfinal/redkali.png)

2. Tabla de enrutamientoSe verificó que Kali Linux pertenece a la red 10.0.2.0/24 y utiliza 10.0.2.1 como puerta de enlace.

![Captura de Enrutamiento](/assets/images/Examenfinal/enrutamiento.png)

3. Descubrimiento de equiposMediante un escaneo de descubrimiento ICMP/ARP se identificaron cuatro hosts activos dentro de la red 10.0.2.0/24. El host 10.0.2.3 fue seleccionado para una enumeración más detallada debido a la cantidad de servicios expuestos.Bashsudo nmap -sn 10.0.2.0/24

![Captura de Descrubrimiento de Equipos](/assets/images/Examenfinal/deteccionequipos.png)

4. Escaneo inicial de puertosSe realizó un escaneo SYN sobre los 1000 puertos TCP más utilizados, identificándose múltiples servicios expuestos, entre ellos SSH, SMB, MySQL, RDP, HTTP/HTTPS, Tomcat, GlassFish y Elasticsearch.

![Captura de Escaneo de Puertos](/assets/images/Examenfinal/escaneoinicial.png)


5. Escaneo completo de puertosSe efectuó un escaneo completo de los 65535 puertos TCP, identificándose una superficie de exposición considerable de más de 40 puertos TCP abiertos. Se detectaron servicios adicionales que no aparecieron en el escaneo inicial de los 1000 puertos.

![Captura de Inteligencia en OpenCTI](/assets/images/Examenfinal/escaneocompleto.png)

6. Identificación de servicios y versionesSe ejecutó un escaneo exhaustivo guardando los resultados en escaneo.txt:Bashsudo nmap -sV -sC -p- 10.0.2.3 -oN escaneo.txt
6.1. SSH, SMB, MySQL y RDP

![Captura de SSH, SMB, MySQL y RDP](/assets/images/Examenfinal/SSH.png)

Se identificaron servicios de administración remota, compartición de archivos y acceso a base de datos, destacando SSH, SMB, MySQL y RDP. Las versiones identificadas permiten realizar posteriormente una evaluación específica de seguridad.

6.2. Servicios Java / GlassFish / Tomcat

![Captura de Servicio Java](/assets/images/Examenfinal/serviciojava.png)

Se identificaron diferentes servicios asociados a servidores de aplicaciones Java, incluyendo Oracle GlassFish 4.0 y Apache Tomcat 8.0.33. La presencia simultánea de varios servicios web amplía la superficie de ataque del servidor y requiere una evaluación individual de cada aplicación.

6.3. Jenkins, WAMPServer y Elasticsearch

![Captura de Jenkins](/assets/images/Examenfinal/jenkins.png)

Se identificaron tres componentes relevantes: Jenkins sobre el puerto 8484, WAMPServer mediante Apache/PHP sobre el puerto 8585 y una instancia de Elasticsearch 1.1.1 en el puerto 9200.

7. Identificación del sistema operativo

Mediante la enumeración SMB se identificó el sistema operativo como Windows Server 2008 R2 Standard Service Pack 1. También se obtuvo el nombre del equipo VAGRANT-2008R2, perteneciente al grupo de trabajo WORKGROUP.

![Captura de Sistema Operativo](/assets/images/Examenfinal/SO.png)

8. Seguridad SMB

Ejecutamos: grep -E -A6 "smb2-security-mode|smb-security-mode" escaneo.txt

![Captura de SMB](/assets/images/Examenfinal/SMB.png)

La enumeración SMB evidenció que la firma SMB no está configurada como obligatoria. Asimismo, se identificó el uso de la cuenta Guest durante la enumeración. Estas condiciones representan configuraciones potencialmente inseguras que incrementan la superficie de exposición del servicio SMB.

9. Enumeración SMB mediante acceso anónimo

Se realizó una consulta al servicio SMB sin proporcionar credenciales. El servidor respondió indicando Anonymous login successful; sin embargo, no fue posible completar el listado de recursos compartidos debido a la negociación con SMB1.

![Captura de Enumeracion](/assets/images/Examenfinal/enumeracion.png)

10. Enumeración del servicio Jenkins

![Captura de servicio Jenkins](/assets/images/Examenfinal/serviciojenkins.png)


Se verificó que el puerto 8484 corresponde a un servidor Jenkins accesible mediante HTTP. La respuesta HTTP permitió identificar Jenkins versión 1.637 y el servidor Jetty utilizado.Bashcurl -I 10.0.2.3:8484
10.1. Captura visual de JenkinsAcceso mediante navegador web en 10.0.2.3:8484.11. Enumeración de Apache/WAMPServerLa consulta HTTP permitió identificar Apache 2.2.21 ejecutándose sobre Windows, junto con PHP 5.3.10. El servidor respondió correctamente con código HTTP 200.Bashcurl -I 10.0.2.3:8585
11.1. Captura visual WAMPServerAcceso mediante navegador web en 10.0.2.3:8585.12. Enumeración de GlassFishSe verificó mediante HTTP que el puerto 8080 corresponde a Oracle GlassFish Server Open Source Edition 4.0, confirmando la información obtenida previamente mediante Nmap.Bashcurl -I 10.0.2.3:8080
12.1. Acceso mediante navegador GlassFishAcceso visual al portal de administración en 10.0.2.3:8080.13. Enumeración de TomcatSe verificó la disponibilidad del servicio web asociado a Apache Tomcat en el puerto 8282. Nmap identificó la versión Apache Tomcat 8.0.33.Bashcurl -I 10.0.2.3:8282
13.1. Acceso mediante navegador TomcatAcceso visual a la interfaz web en 10.0.2.3:8282.14. Enumeración de ElasticsearchSe verificó que el servicio Elasticsearch se encuentra accesible mediante HTTP en el puerto 9200. La respuesta permitió identificar la instancia Overrider y la versión Elasticsearch 1.1.1.Bashcurl 10.0.2.3:9200
15. Estado del clúster ElasticsearchLa consulta del estado del clúster confirmó que Elasticsearch se encuentra operativo con un único nodo.Bashcurl 10.0.2.3:9200/_cluster/health
16. Enumeración de índices ElasticsearchSe consultó el catálogo de índices de Elasticsearch mediante la API _cat/indices. La respuesta permitió identificar los índices disponibles en la instancia y verificar la información que el servicio expone mediante su interfaz REST.Bashcurl 10.0.2.3:9200/_cat/indices?v
17. Verificación adicional de WinRMSe verificó la respuesta del servicio WinRM expuesto en el puerto 5985. Este servicio corresponde a mecanismos de administración remota de Windows y representa una superficie adicional que debe ser protegida y restringida.Bashcurl -I 10.0.2.3:5985/wsman
18. Resumen de servicios encontradosPuertoServicioVersión / Identificación22SSHOpenSSH 7.1139NetBIOSMicrosoft Windows445SMBMicrosoft Windows3306MySQL5.5.203389RDPMicrosoft Terminal Services4848HTTPSGlassFish 4.05985WinRMMicrosoft HTTPAPI8022HTTPApache Tomcat8080HTTPGlassFish 4.08181HTTPSGlassFish 4.08282HTTPTomcat 8.0.338484HTTPJenkins 1.6378585HTTPApache 2.2.21 + PHP 5.3.109200HTTPElasticsearch 1.1.119. Matriz de HallazgosN°HallazgoEvidenciaValoración1Windows Server 2008 R2 SP1Nmap/SMBAlta2SMB expuestoTCP/445Alta3SMB signing no obligatorioNmap NSEAlta4Acceso Guest/Anonymoussmbclient/NmapAlta5MySQL 5.5.20NmapAlta6GlassFish 4.0Nmap/curlAlta7Apache 2.2.21Nmap/curlAlta8PHP 5.3.10Nmap/curlAlta9Tomcat 8.0.33NmapMedia / Alta10Jenkins 1.637Nmap/curlAlta11Elasticsearch 1.1.1Nmap/curlAlta12RDP expuestoTCP/3389Media / Alta13WinRM expuestoTCP/5985Media