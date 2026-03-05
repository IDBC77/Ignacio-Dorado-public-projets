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
---



##2. Actualización inicial del sistema

Antes de instalar herramientas se actualizó el sistema:

sudo apt update
sudo apt upgrade -y

Salida esperada: lista de paquetes actualizados, sin errores.

##3. Instalación de herramientas esenciales

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
##4. Configuración de Git en la VM

Se configuró Git con usuario y correo:

git config --global user.name "IDBC77"
git config --global user.email "ignacio.dorado@live.u-tad.com"
git config --list

Salida esperada:

user.name=IDBC77
user.email=ignacio.dorado@live.u-tad.com
##5. Creación de repositorio local y estructura de documentación
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
##6. Configuración del repositorio remoto y token de autenticación

En GitHub se creó un Personal Access Token (PAT) con permisos repo para poder hacer push desde la VM.

Se configuró el remoto:

git branch -M main
git remote add origin https://github.com/IDBC77/asir-lab-ubuntu-docker.git

Pull inicial para sincronizar cualquier contenido existente:

git pull origin main --allow-unrelated-histories --no-rebase

Push al remoto usando el token como contraseña:

git push -u origin main

Con esto la VM y GitHub quedaron sincronizados y la documentación inicial está disponible remotamente.

