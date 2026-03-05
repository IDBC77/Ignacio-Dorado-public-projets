# Documentación completa: Clonación de VM, instalación de herramientas y configuración de Git

Este documento recoge **todo el proceso realizado hasta el momento** en la VM de Ubuntu 24.04 Server, desde la clonación de la máquina base, la instalación de herramientas esenciales, hasta la configuración completa de Git y sincronización con GitHub.  

> Nota: La configuración de usuarios sudo, RAID, red y SSH se documentará en pasos posteriores. Por inexperiencia inicial, decidí empezar documentando directamente desde la VM la instalación de herramientas y Git, para después poder registrar cada comando en tiempo real.

---

## 1. Clonación de la VM y propiedades

Se partió de una VM base de Ubuntu 24.04.4 LTS y se clonó para trabajar sobre una copia, asegurando que cualquier fallo no afectara a la VM original.  

Propiedades de la VM:

```bash
hostnamectl
# Hostname: ignacioasir-VirtualBox
# Virtualización: Oracle VirtualBox
# Chasis: VM
# Arquitectura: x86-64

lsb_release -a
cat /etc/os-release
# Sistema operativo: Ubuntu 24.04.4 LTS (Noble Numbat)
# Kernel: Linux 6.14.0-37-generic
# Distribución base: Debian

free -h
# Memoria total: 2,9 GiB
# Memoria usada: 1,1 GiB
# Memoria libre: 196 MiB

cat /proc/cpuinfo
# CPU: AMD Ryzen 7 Pro 7735U
# Núcleos: 3
# Hilos: 3
# Caché: 512 KB
 ```

<img width="1301" height="811" alt="Captura de pantalla 2026-03-05 130905" src="https://github.com/user-attachments/assets/f6c0132e-6c3d-487b-8220-33c54ff812f4" />



## 2. Actualización inicial del sistema

Antes de instalar herramientas se actualizó el sistema:

sudo apt update
sudo apt upgrade -y

<img width="635" height="63" alt="image" src="https://github.com/user-attachments/assets/dfcb97c2-a541-4b5a-bd30-6a3dbfe5a395" />


## 3. Instalación de herramientas esenciales

Decidí empezar por instalar herramientas antes de configurar sudo y otros parámetros porque quería documentar todo el proceso desde la VM en tiempo real, sin depender de la configuración de usuarios.

Comando de instalación:

sudo apt install -y htop net-tools iperf3 git curl wget nano vim ufw eza plocate figlet tmux molly-guard

Notas importantes:

exa no está disponible → se utilizó eza.

mlocate no está disponible → se utilizó plocate y se actualizó la base de datos con sudo updatedb.

molly-guard abrió un "Nuevo buffer" en nano, que se cerró sin guardar (CTRL+X → N).

Verificación de herramientas:

htop
iperf3 --version
git --version
curl --version
wget --version
nano --version
vim --version
ufw status
eza --version
plocate foo
figlet Hola
tmux -V

<img width="1307" height="73" alt="image" src="https://github.com/user-attachments/assets/9f9c4bf0-7d0d-4531-9c86-0994ddcc4aeb" />


## 4. Configuración de Git en la VM

Se configuró Git con usuario y correo:
git config --global user.name "IDBC77"
git config --global user.email "ignacio.dorado@live.u-tad.com"
git config --list

<img width="1109" height="438" alt="image" src="https://github.com/user-attachments/assets/bd1097d5-7ada-40ce-88d5-d459778d9f96" />

## 5. Creación de repositorio local y estructura de documentación
cd ~
mkdir asir-lab-ubuntu-docker
cd asir-lab-ubuntu-docker
git init
mkdir docs
touch README.md
touch docs/01-instalacion-vm.md
touch docs/02-instalacion-herramientas.md
touch docs/03-instalacion-docker.md
touch docs/04-creacion-pruebas-snapshots.md
mkdir docs/img
git add .
git commit -m "Estructura inicial de la documentación"
<img width="1303" height="705" alt="image" src="https://github.com/user-attachments/assets/81d3cab0-0be1-49a3-85dc-ede75488ddb6" />

## 6. Configuración del repositorio remoto y token de autenticación

En GitHub se creó un Personal Access Token (PAT) con permisos repo para poder hacer push desde la VM. 

Se configuró el remoto:

git branch -M main
git remote add origin https://github.com/IDBC77/asir-lab-ubuntu-docker.git

Pull inicial para sincronizar cualquier contenido existente:

git pull origin main --allow-unrelated-histories --no-rebase

Push al remoto usando el token como contraseña:

git push -u origin main


Con esto la VM y GitHub quedaron sincronizados y la documentación inicial está disponible remotamente.


<img width="1191" height="299" alt="image" src="https://github.com/user-attachments/assets/e23b0152-efb8-41e1-83e4-422b19734e86" />


<img width="1289" height="466" alt="image" src="https://github.com/user-attachments/assets/064c2215-2775-4afd-98e8-335d63e3d696" />


## 7. Configuración de sudo y seguridad

Antes de modificar permisos, se crea una carpeta de seguridad por si algo falla:

```bash
sudo mkdir -p /root/backup_sudo
sudo chmod 700 /root/backup_sudo
sudo cp /etc/sudoers /root/backup_sudo/sudoers.bak
sudo cp -r /etc/sudoers.d /root/backup_sudo/sudoers.d.bak
sudo adduser idbc
sudo usermod -aG sudo idbc
sudo visudo
# Usuario regular con permisos sudo
idbc ALL=(ALL:ALL) ALL
su - idbc
sudo whoami
```
<img width="1271" height="723" alt="Captura de pantalla 2026-03-05 133042" src="https://github.com/user-attachments/assets/88e33ac6-8e03-47b9-96b4-28602533c48b" />

<img width="1624" height="927" alt="Captura de pantalla 2026-03-05 142747" src="https://github.com/user-attachments/assets/f5dab95d-aecf-4d26-b38e-469899bb662f" />

<img width="1288" height="746" alt="Captura de pantalla 2026-03-05 143331" src="https://github.com/user-attachments/assets/9314484e-474d-450d-8099-3036014b4b00" />

<img width="682" height="127" alt="Captura de pantalla 2026-03-05 144409" src="https://github.com/user-attachments/assets/6aea5abc-17c4-4d8c-b893-bc4b12389db4" />

<img width="1530" height="327" alt="Captura de pantalla 2026-03-05 144419" src="https://github.com/user-attachments/assets/aae2de6c-ad96-4139-b3e1-4188d9f35f69" />

<img width="986" height="306" alt="Captura de pantalla 2026-03-05 144730" src="https://github.com/user-attachments/assets/47366a6b-a8ab-4286-9f53-bdaad34352ca" />

<img width="1526" height="580" alt="Captura de pantalla 2026-03-05 145055" src="https://github.com/user-attachments/assets/8a9ed1a6-127a-4367-9ce2-fe61ac2a638c" />








