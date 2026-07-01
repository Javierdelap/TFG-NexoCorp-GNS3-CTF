# TFG: Entorno Virtual Gamificado para la Auditoría Perimetral y Mitigación de Amenazas (NexoCorp-CTF)

[![GNS3](https://img.shields.io/badge/Platform-GNS3-blue.svg)](https://www.gns3.com/)
[![Docker](https://img.shields.io/badge/Container-Docker-blue.svg)](https://www.docker.com/)
[![pfSense](https://img.shields.io/badge/Firewall-pfSense-red.svg)](https://www.pfsense.org/)
[![Snort](https://img.shields.io/badge/IDS%2FIPS-Snort-brightgreen.svg)](https://www.snort.org/)

## 📝 Descripción del Proyecto

Este repositorio aloja los artefactos de ingeniería, ficheros de topología y scripts de configuración correspondientes al Trabajo Fin de Grado desarrollado para la mención de **Telemática** en el **Grado de Ingeniería de Tecnologías de Telecomunicación (Universidad de Cantabria)**. 

El escenario implementa una arquitectura de *Defensa en Profundidad* basada en la red corporativa de la empresa simulada **NexoCorp**. A través de una narrativa de espionaje industrial coordinada junto al **Centro Nacional de Inteligencia (CNI)**, el entorno plantea un escenario gamificado de tipo **Capturar la Bandera (CTF)** para que los estudiantes consoliden habilidades prácticas tanto ofensivas como defensivas.

---

## 📂 Contenido e Inventario del Proyecto (.gns3)

Siguiendo el mapa de ingeniería establecido, el repositorio y la topología base contienen:

* **Fichero Core:** `NexoCorp-Topology.gns3` (Estructura lógica, enlaces y coordenadas del lienzo).
* **Configuraciones de Red de la Electrónica:**
    * Fichero de respaldo (*backup*) de configuración XML del router/firewall **pfSense**.
    * Scripts de configuración de interfaces y cortafuegos de **OpenWrt** (`/etc/config/network` y `/etc/config/firewall`).
* **Instalación de Herramientas en Kali Linux:** Guía y listado de scripts para el aprovisionamiento de las utilidades ofensivas (*Nmap, Dirb, Sshuttle*).
* **Personalización Estética:** Directorio con los **iconos customizados** asignados de forma manual a cada nodo para representar visualmente el organigrama empresarial.

---

## 🛠️ Requisitos de Software y Enlaces de Descarga

Debido a derechos de propiedad intelectual, los binarios de los sistemas operativos no se alojan en este repositorio. Deben descargarse de forma legítima desde sus fuentes oficiales:

### 1. Entorno de Gestión e Hipervisor (GNS3 VM)
* **VMware Fusion / Workstation:** [Descarga Oficial de VMware](https://www.vmware.com/products/fusion.html) (Para estabilizar la máquina de cómputo).
* **GNS3 (Software de Escritorio):** [Descarga Oficial de GNS3](https://www.gns3.com/software/download).
* **GNS3 VM:** [Descarga de la MV de Gestión](https://github.com/GNS3/gns3-gui/releases) (Debe coincidir con la versión de escritorio).
* **TigerVNC:** [Descarga de TigerVNC](https://tigervnc.org/) (Protocolo gráfico utilizado por GNS3 para interactuar con las interfaces visuales de Kali Linux y los terminales de usuario de forma fluida).

### 2. Appliances y Sistemas Operativos de Red
* **pfSense (Imagen QEMU):** [Descarga Oficial de pfSense ISO](https://www.pfsense.org/download/).
* **OpenWrt (Imagen de Enrutamiento):** [Marketplace de OpenWrt en GNS3](https://www.gns3.com/marketplace/appliances/openwrt-2).
* **Kali Linux (Appliance QEMU):** [Servidor de Imágenes QEMU de Kali](https://cdimage.kali.org/kali-2026.2/kali-linux-2026.2-qemu-amd64.7z).

---

## 🐳 Gestión y Subida de Imágenes Docker Customizadas

Las estaciones de trabajo de los usuarios y los servidores intermedios (`webnexocorp`, `nexocorpdb`, `nexocorpbak`, etc.) se han materializado modificando manualmente contenedores basados en la imagen `webterm` (añadiendo pistas del CTF en archivos de texto, modificando permisos mediante el bit `SUID` y levantando servicios con distintos usuarios).

Dado que estos sistemas de archivos no se suben directamente a GitHub por restricciones de espacio, se gestionan a través de un registro público como **Docker Hub**. 

### Pasos para guardar y subir tus contenedores modificados:

Si realizas cambios dentro de un nodo de usuario en GNS3 y quieres congelarlo y subirlo a tu cuenta de Docker Hub para que sea portable, ejecuta los siguientes comandos en la consola de la **GNS3 VM** (o en tu terminal local si manejas el demonio de Docker):

```bash
# 1. Identificar el ID del contenedor Docker que está corriendo en GNS3
docker ps

# 2. Congelar el estado actual del contenedor creando una nueva imagen local
# Cambia 'ID_CONTENEDOR' por el tuyo y usa tu usuario de Docker Hub
docker commit ID_CONTENEDOR tu_usuario_docker/nexocorp-users:latest

# 3. Iniciar sesión en Docker Hub desde la terminal
docker login -u tu_usuario_docker

# 4. Subir la imagen empaquetada al registro público en la nube
docker push tu_usuario_docker/nexocorp-users:latest
