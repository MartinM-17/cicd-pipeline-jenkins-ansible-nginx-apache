# CI/CD Pipeline con Jenkins y Ansible

Este proyecto configura un pipeline de CI/CD utilizando **Jenkins** y **Ansible** para desplegar un sitio web estático en dos servidores web distintos (Nginx y Apache). El pipeline se activa automáticamente cada vez que se hace un push al repositorio de GitHub.

## Tabla de Contenidos
- [Requisitos](#requisitos)
- [Arquitectura](#arquitectura)
- [Instalación y Configuración](#instalación-y-configuración)
- [Uso](#uso)
- [Contribuciones](#contribuciones)

## Requisitos

- VirtualBox
- Ubuntu Server 22.04 ISO
- Jenkins
- Ansible
- GitHub (usuario: `MartinM-17`)

## Arquitectura

El proyecto utiliza cuatro máquinas virtuales:
1. **Jenkins**: orquesta el pipeline.
2. **Ansible Controller**: ejecuta el playbook de despliegue.
3. **Servidor Nginx**: sirve el sitio web en un entorno con Nginx.
4. **Servidor Apache**: sirve el sitio web en un entorno con Apache.

## Instalación y Configuración

### 1. Configuración de Máquinas Virtuales
Crea las VMs en VirtualBox usando Ubuntu Server 22.04. Asigna los recursos especificados para cada VM en la sección de [requisitos](#requisitos).

### 2. Instalación de Software
- **Jenkins VM**: Instala Jenkins y sus plugins para GitHub y Ansible.
- **Ansible Controller**: Instala Ansible.
- **Servidores Nginx y Apache**: Instala los respectivos servidores web.

### 3. Configuración de Jenkins y GitHub
- Configura un repositorio en GitHub y sube el archivo `index.html`.
- Configura Jenkins para activar el pipeline con un webhook de GitHub.

### 4. Playbook de Ansible
- Crea un playbook de Ansible que clona el repositorio y despliega el contenido en ambos servidores.

## Uso

Realiza un cambio en `index.html` y súbelo a GitHub. Jenkins activará el pipeline, desplegando automáticamente la actualización en ambos servidores.

## Contribuciones

Este es un proyecto en evolución. Las contribuciones son bienvenidas mediante pull requests.
