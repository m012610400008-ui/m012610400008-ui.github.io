---
title: "Lab 01 - Instalación de Metasploitable 3"
date: 2026-05-24 20:15:00 -0500
categories: [lab, setup]
tags: [metasploitable, virtualbox, kali]
---

## Creación e Importación de la VM Metasploitable 3

Metasploitable 3 es una máquina virtual basada en Windows Server 2008 R2 diseñada intencionalmente con múltiples vulnerabilidades de seguridad. Su función principal en este laboratorio es actuar como entorno controlado (máquina víctima) para realizar de manera segura escaneos de puertos, auditorías y pruebas de explotación sin generar riesgos en sistemas reales.

### Diferencia clave entre entornos .ova y .iso
* **Archivos .ova (Open Virtual Appliance):** Son paquetes preconfigurados que contienen la máquina virtual completamente construida (sistema operativo instalado, tamaño de disco fijo y memoria RAM asignada). Solo requieren ser importados directamente a VirtualBox.
* **Archivos .iso:** Son imágenes de disco óptico vírgenes. No contienen hardware virtual definido, por lo que se requiere construir la máquina manualmente desde cero en el panel de VirtualBox y montar esta imagen como un CD/DVD de arranque para instalar el sistema.

> **Nota de Laboratorio:** Dado que los recursos provistos en este curso corresponden a paquetes `.ova`, el proceso se realizó mediante una importación directa, configurándose el almacenamiento de forma automática con su propio disco virtual preinstalado y prescindiendo del montaje manual de un archivo `.iso`.

![Creación de la VM en VirtualBox]({{ site.baseurl }}/assets/images/lab01/captura_kali.png)

![ISO montado en la VM]({{ site.baseurl }}/assets/images/lab01/captura_win_iso.png)

---

## Configuración de red (NatNetwork)

Para lograr que el laboratorio funcione de manera óptima, ambas máquinas virtuales deben interactuar en un segmento común pero aislado del exterior.

### Justificación del modo de red seleccionado:
Se seleccionó la opción de **NatNetwork** (Red NAT) basándonos en los siguientes criterios técnicos fijados en la clase:
* **Aislamiento y Seguridad:** Al tratarse de una máquina víctima vulnerable, usar un modo como *Bridged* (Adaptador Puente) expondría el servidor a la red física de la universidad o infraestructura local, lo cual representa un alto riesgo de seguridad.
* **Interconectividad:** El modo *NAT clásico* aísla cada equipo de forma independiente, impidiendo que Kali Linux logre "ver" o atacar al objetivo. El modo *NatNetwork* crea un switch virtual intermedio que permite la comunicación mutua directa manteniendo el entorno protegido.
* **Salida a Internet:** Facilita que las máquinas tengan salida regulada hacia internet a través del host para la descarga de herramientas o actualizaciones si es requerido.

---

## Verificación de conectividad

Tras levantar ambas máquinas virtuales en el segmento correspondiente, procedimos a validar que se encuentren en la misma subred interna compartida.

### Direcciones IP Asignadas:

| Máquina | IP asignada |
|:--------|:------------|
| Kali Linux (atacante) | 10.0.2.10 |
| Metasploitable 3 (víctima) | 10.0.2.3 |

A través de la consola del atacante (Kali Linux), enviamos paquetes ICMP a la máquina objetivo obteniendo respuestas exitosas en tiempo real, confirmando el enlace.

![Ping exitoso]({{ site.baseurl }}/assets/images/lab01/ping_ok.png)