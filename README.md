# CI/CD Pipeline con Jenkins, Ansible, Nginx y Apache

## Descripción
Este proyecto implementa un pipeline de CI/CD utilizando **Jenkins**, **Ansible**, **Nginx** y **Apache** en un entorno con máquinas virtuales (VM) configuradas en VirtualBox. El objetivo es automatizar el despliegue de contenido web (archivo `index.html`) en servidores web Nginx y Apache a través de Ansible, activado mediante cambios en un repositorio de GitHub.

## Objetivos
- Configurar y gestionar un pipeline de CI/CD con Jenkins y Ansible.
- Automatizar el despliegue de contenido en servidores web Nginx y Apache.
- Utilizar webhooks de GitHub para activar despliegues automáticos con Jenkins.
- Exponer Jenkins a internet de forma segura usando **ngrok**.

## Arquitectura
El proyecto consta de las siguientes VMs:
1. **Jenkins**: Servidor de integración continua, encargado de activar el pipeline.
2. **Ansible Controller**: Máquina para ejecutar los playbooks de Ansible.
3. **Servidor Web Nginx**: Servidor para recibir el contenido desplegado.
4. **Servidor Web Apache**: Servidor alternativo para el despliegue del contenido.

## Requisitos Previos
- VirtualBox instalado en tu sistema.
- Ubuntu Server 24.04 ISO para crear las VMs.
- Acceso a GitHub y ngrok.
- Conexión a internet en las VMs para instalar los paquetes necesarios.

## Instalación
### Paso 1: Preparar el entorno
1. Descarga e instala **VirtualBox**.
2. Crea las cuatro VMs necesarias:
   - Jenkins: 4GB RAM, 20GB HDD.
   - Ansible Controller: 2GB RAM, 10GB HDD.
   - Nginx y Apache: 1GB RAM, 10GB HDD cada una.

### Paso 2: Configurar los servidores web
- **En la VM Nginx**:
  ```bash
  sudo apt update && sudo apt install nginx -y
  sudo rm /usr/share/nginx/html/index.html
  ```
- **En la VM Apache**:
  ```bash
  sudo apt update && sudo apt install apache2 -y
  sudo rm /var/www/html/index.html
  ```

### Paso 3: Configurar Ansible
- Instala Ansible y Git:
  ```bash
  sudo apt update && sudo apt install ansible git -y
  ```
- Configura el inventario de Ansible (`hosts.ini`):
  ```ini
  [nginx]
  nginx ansible_host=192.168.1.70 ansible_user=martin ansible_ssh_private_key_file=~/.ssh/id_rsa

  [apache]
  apache ansible_host=192.168.1.60 ansible_user=martin ansible_ssh_private_key_file=~/.ssh/id_rsa

  [webservers:children]
  nginx
  apache
  ```

### Paso 4: Crear el Playbook de Ansible
Crea el archivo `deploy.yml` en la VM de Ansible:
```yaml
- name: Deploy Static Website
  hosts: all
  become: yes
  tasks:
    - name: Clonar repositorio
      ansible.builtin.git:
        repo: https://github.com/MartinM-17/cicd-pipeline-jenkins-ansible-nginx-apache.git
        dest: /var/www/html
        update: yes

    - name: Copiar archivo index.html a Nginx
      copy:
        src: /var/www/html/index.html
        dest: /usr/share/nginx/html/index.html
      when: "'nginx' in group_names"

    - name: Copiar archivo index.html a Apache
      copy:
        src: /var/www/html/index.html
        dest: /var/www/html/index.html
      when: "'apache' in group_names"
```
- when: "'nginx' in group_names": Verifica si el host pertenece al grupo nginx en hosts.ini.
- when: "'apache' in group_names": Verifica si el host pertenece al grupo apache en hosts.ini.

### Beneficios de esta Configuración
- Claridad: Separamos las tareas de despliegue en Nginx y Apache usando grupos.
- Flexibilidad: Permite manejar los servidores de manera independiente o conjunta en el playbook.
- Mantenimiento: Facilita la modificación futura del inventario y el playbook.

### Paso 5: Configurar Jenkins
- Instala Jenkins en la VM:
  ```bash
  sudo apt update
  sudo apt install openjdk-11-jdk -y
  sudo apt install jenkins -y
  sudo systemctl start jenkins
  ```
- Configura Jenkins para conectarse al Ansible Controller usando SSH.
- Instala ngrok para exponer Jenkins a internet:
  ```bash
  sudo snap install ngrok
  ngrok config add-authtoken <AUTHTOKEN>
  ngrok http 8080
  ```

### Paso 6: Crear el Pipeline en Jenkins
- Configura un nuevo pipeline en Jenkins usando el script del repositorio:
  - URL del repositorio: `https://github.com/MartinM-17/cicd-pipeline-jenkins-ansible-nginx-apache.git`
  - Ruta del script: `Jenkinsfile`

## Jenkinsfile
```groovy
pipeline {
    agent any
    stages {
        stage('Clonar repositorio') {
            steps {
                git 'https://github.com/MartinM-17/cicd-pipeline-jenkins-ansible-nginx-apache.git'
            }
        }
        stage('Desplegar con Ansible') {
            steps {
                sh 'ansible-playbook -i hosts.ini deploy.yml'
            }
        }
    }
}
```

## Prueba del Pipeline
1. Realiza cambios en `index.html` y haz push al repositorio.
2. Verifica que Jenkins ejecute el pipeline y Ansible despliegue los cambios en los servidores Nginx y Apache.
3. Accede a las IPs de los servidores para ver el contenido actualizado.

## Resultado Esperado
El archivo `index.html` se actualiza automáticamente en los servidores web Nginx y Apache cada vez que se realiza un push al repositorio de GitHub.

## Autor
- [MartinM-17](https://github.com/MartinM-17)

## Referencias
- [Documentación de Ansible](https://docs.ansible.com/)
- [Documentación de Jenkins](https://www.jenkins.io/doc/)
- [ngrok - Exposición segura](https://ngrok.com/)
