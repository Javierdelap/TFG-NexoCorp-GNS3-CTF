# TFG: Entorno Virtual Gamificado para la Auditoría Perimetral y Mitigación de Amenazas (NexoCorp-CTF)

[![GNS3](https://img.shields.io/badge/Platform-GNS3-blue.svg)](https://www.gns3.com/)
[![Docker](https://img.shields.io/badge/Container-Docker-blue.svg)](https://www.docker.com/)
[![pfSense](https://img.shields.io/badge/Firewall-pfSense-red.svg)](https://www.pfsense.org/)
[![Snort](https://img.shields.io/badge/IDS%2FIPS-Snort-brightgreen.svg)](https://www.snort.org/)

## 📝 Descripción del Proyecto

Este repositorio aloja los artefactos de ingeniería, ficheros de topología y scripts de configuración correspondientes al Trabajo Fin de Grado desarrollado para la mención de **Telemática** en el **Grado de Ingeniería de Tecnologías de Telecomunicación (Universidad de Cantabria)**. 

El proyecto consiste en el diseño, despliegue y validación analítica de un laboratorio virtual docente altamente segmentado y basado en arquitectura de *Defensa en Profundidad*. A través de una narrativa inmersiva de espionaje industrial bajo el amparo del **Centro Nacional de Inteligencia (CNI)**, el entorno plantea un escenario gamificado de tipo **Capturar la Bandera (CTF)**. El objetivo es que los estudiantes consoliden habilidades prácticas tanto ofensivas (*pentesting*, movimiento lateral, pivotaje) como defensivas (sintonización fina de firmas analíticas en sistemas IDS/IPS y control perimetral).

---

## 📐 Arquitectura de la Topología (NexoCorp)

El escenario implementa una arquitectura en árbol clásica (*Three-Homed Firewall Architecture*) dividida en tres zonas de confianza lógicas e independientes:

1. **Zona WAN (Segmento de Ataque):** Aloja la estación del auditor (*BadGuy* en Kali Linux). Interconecta con la pata externa del cortafuegos (`192.168.122.213`).
2. **Zona Desmilitarizada / DMZ (`192.168.20.0/24`):** Segmento controlado por **pfSense** que aloja los servicios públicos modificados de la empresa (`webnexocorp`, `nexocorpbak`, etc.). El tráfico entrante es monitorizado de forma síncrona por **Snort**.
3. **Zona LAN Protegida (`192.168.10.0/24`):** Núcleo confidencial de la organización aislado este-oeste por el enrutador interno **OpenWrt**. Custodia el servidor de bases de datos (`nexocorpdb`) y las estaciones del organigrama corporativo (CEO Charlie, Admin Alice, etc.).

---

## 🛠️ Requisitos de Software y Enlaces de Descarga

Para replicar y estabilizar este laboratorio telemático, se requiere la descarga e instalación de los siguientes componentes legítimos:

### 1. Hipervisor y Motor de Simulación
* **VMware Fusion / Workstation:** [Descarga Oficial de VMware](https://www.vmware.com/products/fusion.html) (Necesario para levantar la máquina virtual de gestión).
* **GNS3 (Software de Escritorio):** [Descarga Oficial de GNS3](https://www.gns3.com/software/download) (Entorno de diseño).
* **GNS3 VM:** [Descarga Oficial de GNS3 VM](https://github.com/GNS3/gns3-gui/releases) (Debe coincidir estrictamente con la versión de tu software de escritorio).

### 2. Appliances y Sistemas Operativos de Red
* **pfSense (Imagen QEMU):** [Descarga Oficial de pfSense](https://www.pfsense.org/download/) (Se recomienda la arquitectura AMD64 en formato ISO para su instalación).
* **OpenWrt (Imagen de Enrutamiento):** [Descarga Oficial de OpenWrt x86](https://openwrt.org/docs/guide-user/virtualization/gns3) (Instanciado como nodo x86 genérico).
* **Kali Linux (Appliance GNS3):** [Descarga del Marketplace de GNS3](https://www.gns3.com/marketplace/appliances/kali-linux) (Optimizado para QEMU).

---

## 🐳 Contenedores Docker Personalizados (NexoCorp Nodes)

Todas las estaciones de usuario y servidores web se han materializado mediante la contenedorización ligera de Docker partiendo de la plantilla base `webterm`. Cada nodo fue modificado manualmente para inyectar fallos lógicos deliberados, dependencias de red, usuarios únicos del organigrama empresarial y las correspondientes *flags* del CTF.

Las imágenes finales congeladas mediante `docker commit` se encuentran publicadas en el registro público de Docker Hub y pueden ser desplegadas en tu servidor local ejecutando:

* **Servidor Web Corporativo (DMZ):** `docker pull tu_usuario_docker/webnexocorp:latest`
* **Servidor de Base de Datos (LAN):** `docker pull tu_usuario_docker/nexocorpdb:latest`
* **Estación de Copias de Seguridad (DMZ):** `docker pull tu_usuario_docker/nexocorpbak:latest`
* **Estaciones de Organigrama (LAN):** `docker pull tu_usuario_docker/nexocorp-users:latest`

---

## 🛡️ Reglas de Seguridad Analítica Implementadas (Snort)

El motor de detección e inspección profunda en la interfaz de la DMZ cuenta con las siguientes firmas personalizadas (`custom rules`) encargadas de registrar las alertas del escenario práctico:

```snort
# Regla 1: Identificación de ráfagas SYN vacías (Denegación de Servicio)
alert tcp any any -> 192.168.20.20 80 (msg:"ALERTA CNI: Intento de TCP SYN Flood detectado"; flags:S; detection_filter:track by_dst, count 300, seconds 3; sid:1000003; rev:3;)

# Regla 2: Enumeración automatizada mediante control de flags ACK (Directory Fuzzing)
alert tcp any any -> 192.168.20.20 80 (msg:"CNI: Directory Fuzzing por rafaga detectado"; flags:A+; detection_filter:track by_src, count 80, seconds 10; sid:1000010; rev:3;)

# Regla 3: Interceptación volumétrica de salida establecida (Exfiltración de Pegasus)
alert tcp 192.168.20.0/24 any -> any any (msg:"ALERTA EXFILTRACION: Fuga masiva de volumen de datos desde la DMZ"; flow:established,from_server; detection_filter:track by_src, count 100, seconds 5; sid:1000020; rev:5;)
