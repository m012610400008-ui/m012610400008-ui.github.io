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

---

## 2. Tabla de enrutamiento

Se verificó que Kali Linux pertenece a la red `10.0.2.0/24` y utiliza `10.0.2.1` como puerta de enlace.

![Captura de Enrutamiento](/assets/images/Examenfinal/enrutamiento.png)

---

## 3. Descubrimiento de equipos

Mediante un escaneo de descubrimiento ICMP/ARP se identificaron cuatro hosts activos dentro de la red 10.0.2.0/24. El host 10.0.2.3 fue seleccionado para una enumeración más detallada debido a la cantidad de servicios expuestos.Bashsudo nmap -sn 10.0.2.0/24

![Captura de Descrubrimiento de Equipos](/assets/images/Examenfinal/deteccionequipos.png)

## 4. Escaneo inicial de puertos

Se realizó un escaneo SYN sobre los 1000 puertos TCP más utilizados, identificándose múltiples servicios expuestos, entre ellos SSH, SMB, MySQL, RDP, HTTP/HTTPS, Tomcat, GlassFish y Elasticsearch.

![Captura de Escaneo de Puertos](/assets/images/Examenfinal/escaneoinicial.png)

---

## 5. Escaneo completo de puertos

Se efectuó un escaneo completo de los 65535 puertos TCP, identificándose una superficie de exposición considerable de más de 40 puertos TCP abiertos. Se detectaron servicios adicionales que no aparecieron en el escaneo inicial de los 1000 puertos.

![Captura de Inteligencia en OpenCTI](/assets/images/Examenfinal/escaneocompleto.png)

---

## 6. Identificación de servicios y versiones

Se ejecutó un escaneo exhaustivo guardando los resultados en escaneo.txt:Bashsudo nmap -sV -sC -p- 10.0.2.3 -oN escaneo.txt

### 6.1. SSH, SMB, MySQL y RDP

![Captura de SSH, SMB, MySQL y RDP](/assets/images/Examenfinal/SSH.png)

---

Se identificaron servicios de administración remota, compartición de archivos y acceso a base de datos, destacando SSH, SMB, MySQL y RDP. Las versiones identificadas permiten realizar posteriormente una evaluación específica de seguridad.

### 6.2. Servicios Java / GlassFish / Tomcat

![Captura de Servicio Java](/assets/images/Examenfinal/serviciojava.png)

---

Se identificaron diferentes servicios asociados a servidores de aplicaciones Java, incluyendo Oracle GlassFish 4.0 y Apache Tomcat 8.0.33. La presencia simultánea de varios servicios web amplía la superficie de ataque del servidor y requiere una evaluación individual de cada aplicación.

### 6.3. Jenkins, WAMPServer y Elasticsearch

![Captura de Jenkins](/assets/images/Examenfinal/jenkins.png)

Se identificaron tres componentes relevantes: Jenkins sobre el puerto 8484, WAMPServer mediante Apache/PHP sobre el puerto 8585 y una instancia de Elasticsearch 1.1.1 en el puerto 9200.

---

## 7. Identificación del sistema operativo

Mediante la enumeración SMB se identificó el sistema operativo como Windows Server 2008 R2 Standard Service Pack 1. También se obtuvo el nombre del equipo VAGRANT-2008R2, perteneciente al grupo de trabajo WORKGROUP.

![Captura de Sistema Operativo](/assets/images/Examenfinal/SO.png)

---

## 8. Seguridad SMB

Ejecutamos: grep -E -A6 "smb2-security-mode|smb-security-mode" escaneo.txt

![Captura de SMB](/assets/images/Examenfinal/SMB.png)

La enumeración SMB evidenció que la firma SMB no está configurada como obligatoria. Asimismo, se identificó el uso de la cuenta Guest durante la enumeración. Estas condiciones representan configuraciones potencialmente inseguras que incrementan la superficie de exposición del servicio SMB.

---

## 9. Enumeración SMB mediante acceso anónimo

Se realizó una consulta al servicio SMB sin proporcionar credenciales. El servidor respondió indicando Anonymous login successful; sin embargo, no fue posible completar el listado de recursos compartidos debido a la negociación con SMB1.

![Captura de Enumeracion](/assets/images/Examenfinal/enumeracion.png)

---

## 10. Enumeración del servicio Jenkins

![Captura de servicio Jenkins](/assets/images/Examenfinal/serviciojenkins.png)

Se verificó que el puerto 8484 corresponde a un servidor Jenkins accesible mediante HTTP. La respuesta HTTP permitió identificar Jenkins versión 1.637 y el servidor Jetty utilizado.Bashcurl -I 10.0.2.3:8484

---

### 10.1 Captura visual de Jenkins

Acceso mediante navegador web en 10.0.2.3:8484.

![Captura de jenkins firefox](/assets/images/Examenfinal/jenkinsfirefox.png)

---

## 11. Enumeración de Apache/WAMPServer

La consulta HTTP permitió identificar Apache 2.2.21 ejecutándose sobre Windows, junto con PHP 5.3.10. El servidor respondió correctamente con código HTTP 200.Bashcurl -I 10.0.2.3:8585

![Captura de Enumeración de Apache](/assets/images/Examenfinal/apache.png)

---

## 12. Captura visual WAMPServer

Acceso mediante navegador web en 10.0.2.3:8585.12. Enumeración de GlassFishSe verificó mediante HTTP que el puerto 8080 corresponde a Oracle GlassFish Server Open Source Edition 4.0, confirmando la información obtenida previamente mediante Nmap.Bashcurl -I 10.0.2.3:8080

![Captura de WAMPServer](/assets/images/Examenfinal/wampserver.png)

---

## 13. Acceso mediante navegador GlassFish

Acceso visual al portal de administración en 10.0.2.3:8080.

![Captura de GlassFish](/assets/images/Examenfinal/glassfish.png)

---

## 14. Enumeración de Tomcat

Se verificó la disponibilidad del servicio web asociado a Apache Tomcat en el puerto 8282. Nmap identificó la versión Apache Tomcat 8.0.33.

![Captura de tomcat](/assets/images/Examenfinal/tomcat.png)

---

### 13.1 Acceso mediante navegador Tomcat

Acceso visual a la interfaz web en 10.0.2.3:8282.

![Captura de navegadortomcat](/assets/images/Examenfinal/navegadortomcat.png)


## 14. Enumeración de Elasticsearch

Se verificó que el servicio Elasticsearch se encuentra accesible mediante HTTP en el puerto 9200. La respuesta permitió identificar la instancia Overrider y la versión Elasticsearch 1.1.1.Bashcurl 10.0.2.3:9200

![Captura de Elasticsearch](/assets/images/Examenfinal/elasticsearch.png)

---

## 15. Estado del clúster Elasticsearch

La consulta del estado del clúster confirmó que Elasticsearch se encuentra operativo con un único nodo.Bashcurl 10.0.2.3:9200/_cluster/health

![Captura de Cluster](/assets/images/Examenfinal/cluster.png)

---

## 16. Enumeración de índices Elasticsearch

Se consultó el catálogo de índices de Elasticsearch mediante la API _cat/indices. La respuesta permitió identificar los índices disponibles en la instancia y verificar la información que el servicio expone mediante su interfaz REST.Bashcurl 10.0.2.3:9200/_cat/indices?v

![Captura de Indices](/assets/images/Examenfinal/indices.png)

---

## 17. Verificación adicional de WinRM

Se verificó la respuesta del servicio WinRM expuesto en el puerto 5985. Este servicio corresponde a mecanismos de administración remota de Windows y representa una superficie adicional que debe ser protegida y restringida.Bashcurl -I 10.0.2.3:5985/wsman

![Captura de WINRM](/assets/images/Examenfinal/WINRM.png)

---

## 18. Resumen de servicios encontrados

| Puerto | Servicio | Versión/Identificación |
| :--- | :---: | :---: |
| 22 | SSH | OpenSSH 7.1 |
| 139 | NetBIOS | Microsoft Windows |
| 445 | SMB | Microsoft Windows |
| 3306 | MySQL | 5.5.20 |
| 3389 | RDP | Microsoft Terminal Services |
| 4848 | HTTPS | GlassFish 4.0 |
| 5985 | WinRM | Microsoft HTTPAPI |
| 8022 | HTTP | Apache Tomcat |
| 8080 | HTTP| GlassFish 4.0 |
| 8181 | HTTPS| GlassFish 4.0 |
| 8282 | HTTP | Tomcat 8.0.33 |
| 8484 | HTTP | Jenkins 1.637 |
| 8585 | HTTP | Apache 2.2.21 + PHP 5.3.10 |
| 9200| HTTP | Elasticsearch 1.1.1 |

## 19. Matriz de Hallazgos

| N° | Hallazgo | Evidencia | Valoración | Valoración |
| :--- | :---: | :---: | :---: |
| 1 | Windows Server 2008 R2 SP1 | Nmap/SMB | Alta |
| 2 | SMB expuesto | TCP/445 | Alta |
| 3 | SMB signing no obligatorio | Nmap NSE | Alta |
| 4 | Acceso Guest/Anonymous | smbclient/Nmap | Alta |
| 5 | MySQL 5.5.20 | Nmap | Alta |
| 6 | GlassFish 4.0 | Nmap/curl | Alta |
| 7 | Apache 2.2.21 | Nmap/curl | Alta |
| 8 | PHP 5.3.10 | Nmap/curl | Alta |
| 9 | Tomcat 8.0.33| Nmap | Media/ Alta |
| 10 | Jenkins 1.637| Nmap/curl | Alta |
| 11 | Elasticsearch 1.1.1 | Nmap/curl | Alta |
| 12 | RDP expuesto | TCP/3389 | Media /Alta |
| 13 | WinRM expuesto | TCP/5985 | Media |

---