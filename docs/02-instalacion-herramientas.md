# 1 Instalación de Portairner
```bash
# ================================
# Instalación de Portainer en Docker
# ================================

# 1. Actualizar repositorios y sistema (opcional pero recomendado)
sudo apt update && sudo apt upgrade -y

# 2. Verificar versiones instaladas
lsb_release -a        # Muestra versión de Ubuntu
docker --version       # Muestra versión de Docker

# 3. Crear una red Docker para Portainer (opcional pero recomendado)
docker network create portainer_network

# 4. Ejecutar Portainer en Docker
docker run -d \
  -p 9000:9000 \
  --name portainer \
  --restart=always \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v portainer_data:/data \
  --network portainer_network \
  portainer/portainer-ce:latest

# 5. Verificar que el contenedor esté corriendo
docker ps

# 6. Obtener la IP de la VM (para acceder desde tu navegador)
ip a

# 7. Acceder a Portainer desde un navegador
# Abrir en tu PC anfitrión o en otra VM:
# http://<IP_DE_LA_VM>:9000
# La primera vez pedirá crear un usuario admin y contraseña.
```


<img width="1541" height="891" alt="image" src="https://github.com/user-attachments/assets/d8e371e1-671b-4150-9e34-237185986595" />

<img width="1800" height="951" alt="image" src="https://github.com/user-attachments/assets/d51650b7-dcdf-4280-ae26-a8ba6aa71bfc" />


#  2 Desplegar stack de Wordpress + BD

## 2.1 

```bash
# ================================
# DESPLIEGUE WORDPRESS + MYSQL
# USANDO DOCKER COMPOSE (CLI)
# ================================

# 1. Crear directorio para la stack
mkdir -p ~/docker/wordpress_stack

# 2. Entrar en el directorio
cd ~/docker/wordpress_stack

# 3. Crear el fichero docker-compose.yml
nano docker-compose.yml

# 4. Pegar la siguiente configuración YAML

version: '3.9'

services:

  wordpress:
    image: wordpress:latest
    container_name: wp_frontend
    restart: always
    ports:
      - "8080:80"
    environment:
      WORDPRESS_DB_HOST: db:3306
      WORDPRESS_DB_USER: wp_user
      WORDPRESS_DB_PASSWORD: wp_pass
      WORDPRESS_DB_NAME: wp_db
    volumes:
      - wp_data:/var/www/html
    depends_on:
      - db

  db:
    image: mysql:8.0
    container_name: wp_db
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: root_pass
      MYSQL_DATABASE: wp_db
      MYSQL_USER: wp_user
      MYSQL_PASSWORD: wp_pass
    volumes:
      - db_data:/var/lib/mysql

volumes:
  wp_data:
  db_data:


# 5. Guardar y salir de nano
# CTRL + X
# Y
# ENTER

# 6. Desplegar la stack
docker compose up -d

# 7. Verificar contenedores
docker ps

# 8. Acceder a WordPress desde el navegador
# http://IP_VM:8080
```
<img width="1448" height="228" alt="image" src="https://github.com/user-attachments/assets/038018bf-b94b-4b4b-ba8c-58de97cd1e5e" />


<img width="1899" height="976" alt="image" src="https://github.com/user-attachments/assets/8b02fcf1-0cb5-4249-944c-8407c8f979bf" />

Lo primero decir que añadir stacks sólo se puede desde la maquina donde tienes el contenedor docker, lo cual tiene sentido.

<img width="1899" height="1010" alt="image" src="https://github.com/user-attachments/assets/79e76796-d847-47c3-a844-d0bccf923b73" />
<img width="1525" height="818" alt="image" src="https://github.com/user-attachments/assets/ee3480d6-df63-46ce-a000-10cd4d079d87" />

version: '3.9'

services:

  wordpress:
    image: wordpress:latest
    container_name: wp_frontend
    restart: always
    ports:
      - "8080:80"
    environment:
      WORDPRESS_DB_HOST: db:3306
      WORDPRESS_DB_USER: wp_user
      WORDPRESS_DB_PASSWORD: wp_pass
      WORDPRESS_DB_NAME: wp_db
    volumes:
      - wp_data:/var/www/html
    depends_on:
      - db

  db:
    image: mysql:8.0
    container_name: wp_db
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: root_pass
      MYSQL_DATABASE: wp_db
      MYSQL_USER: wp_user
      MYSQL_PASSWORD: wp_pass
    volumes:
      - db_data:/var/lib/mysql

volumes:
  wp_data:
  db_data:

  <img width="1608" height="959" alt="image" src="https://github.com/user-attachments/assets/daf8b39b-cd9d-4f0b-b211-2b54f2e7319f" />

  
<img width="1563" height="865" alt="image" src="https://github.com/user-attachments/assets/7a856c13-19e6-4a57-bd58-ca9bf6168be5" />

<img width="1554" height="792" alt="image" src="https://github.com/user-attachments/assets/afcd1e17-f419-4912-b1a7-1ce10ff0db8f" />

<img width="727" height="631" alt="image" src="https://github.com/user-attachments/assets/0a072159-01af-4651-bd90-d42ec93ec154" />
aso 1 — Desplegar MySQL en VM2

Crear el directorio del proyecto:

mkdir -p ~/docker/mysql_wordpress
cd ~/docker/mysql_wordpress

Crear el fichero docker-compose.yml:

nano docker-compose.yml

Contenido del archivo:

services:

  db:
    image: mysql:8.0
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: root_pass
      MYSQL_DATABASE: wp_db
      MYSQL_USER: wp_user
      MYSQL_PASSWORD: wp_pass
    ports:
      - "3306:3306"
    volumes:
      - db_data:/var/lib/mysql

volumes:
  db_data:

Desplegar el contenedor:

docker compose up -d

Verificar que el contenedor está activo:

docker ps
Paso 2 — Obtener la IP de VM2

En VM2 ejecutar:

ip a

Ejemplo:

192.168.1.58

Esta IP será usada por WordPress para conectarse a MySQL.

Paso 3 — Desplegar WordPress en VM1

Crear el directorio:

mkdir -p ~/docker/wp_frontend
cd ~/docker/wp_frontend

Crear el archivo de configuración:

nano docker-compose.yml

Contenido:

services:

  wordpress:
    image: wordpress:latest
    restart: always
    ports:
      - "8080:80"
    environment:
      WORDPRESS_DB_HOST: 192.168.1.58:3306
      WORDPRESS_DB_USER: wp_user
      WORDPRESS_DB_PASSWORD: wp_pass
      WORDPRESS_DB_NAME: wp_db
    volumes:
      - wp_data:/var/www/html

volumes:
  wp_data:

Desplegar WordPress:

docker compose up -d

Comprobar contenedores:

docker ps
Paso 4 — Acceder a WordPress

Desde el navegador acceder a:

http://192.168.1.56:8080

WordPress se conectará a la base de datos que está en VM2.

Comprobación de conectividad entre VMs

Desde VM1 comprobar conexión a MySQL:

ping 192.168.1.58

También se puede comprobar el puerto:

nc -zv 192.168.1.58 3306


<img width="1919" height="1043" alt="image" src="https://github.com/user-attachments/assets/07e4b593-1d9f-4553-9c4c-3eefd78370ec" />

<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/ce331cb5-4683-4f2c-9049-1c9903291ade" />




<img width="1901" height="955" alt="image" src="https://github.com/user-attachments/assets/fdaa7ecd-acd2-4379-a334-5ba0ad23380a" />

<img width="1913" height="950" alt="image" src="https://github.com/user-attachments/assets/435e55ff-28ee-4597-be63-5e363b57e09d" />


