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

## 2.2 Portainer usando el docker-compose anterior

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


<img width="727" height="631" alt="image" src="https://github.com/user-attachments/assets/0a072159-01af-4651-bd90-d42ec93ec154" />

## 2.3 Desplegando Front de WP en VM1 y BD en VM2



Paso 1 — Desplegar MySQL en VM2
````bash
#Crear el directorio del proyecto:

mkdir -p ~/docker/mysql_wordpress
cd ~/docker/mysql_wordpress

#Crear el fichero docker-compose.yml:

nano docker-compose.yml

#Contenido del archivo:

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
# guardas y sales
# Desplegar el contenedor:

docker compose up -d

#Verificar que el contenedor está activo:

docker ps
````

Paso 2 — Obtener la IP de VM2

````bash
#En VM2 ejecutar:
ip a
````
Esta IP será usada por WordPress para conectarse a MySQL.

Paso 3 — Desplegar WordPress en VM1

````bash
Crear el directorio:

mkdir -p ~/docker/wp_frontend
cd ~/docker/wp_frontend

#Crear el archivo de configuración:

nano docker-compose.yml

# Contenido:

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

# Desplegar WordPress:

docker compose up -d

# Comprobar contenedores:

docker ps

````

Paso 4 — Acceder a WordPress


Desde el navegador acceder a:

http://192.168.1.56:8080

WordPress se conectará a la base de datos que está en VM2.

Comprobación de conectividad entre VMs
````bash
# Desde VM1 comprobar conexión a MySQL:

ping 192.168.1.58

# También se puede comprobar el puerto:

nc -zv 192.168.1.58 3306
````

<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/ce331cb5-4683-4f2c-9049-1c9903291ade" />

Configurar VM2 (Prometheus + Node Exporter)
````bash
#Crear carpeta del proyecto:

mkdir ~/monitoring && cd ~/monitoring

#Crear docker-compose.yml:

version: '3.9'

services:
  prometheus:
    image: prom/prometheus
    container_name: prometheus
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
    ports:
      - "9090:9090"

  node_exporter:
    image: prom/node-exporter
    container_name: node_exporter
    ports:
      - "9100:9100"

#Crear prometheus.yml:

global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'node_exporter'
    static_configs:
      - targets: ['localhost:9100']

Arrancar los contenedores:

# docker compose up -d

# Verificar:

curl http://localhost:9100/metrics
curl http://localhost:9090/-/ready
2️ Configurar VM1 (Grafana)

# Crear carpeta y docker-compose.yml:

version: '3.9'

services:
  grafana:
    image: grafana/grafana
    container_name: grafana
    ports:
      - "3000:3000"

# Arrancar contenedor:

docker compose up -d
````

Acceder a Grafana en navegador:

http://<IP_VM1>:3000

Usuario: admin

Contraseña: admin

Añadir Prometheus como Data Source en Grafana:

URL: http://<IP_VM2>:9090

Guardar y probar conexión.

# 3️ Instalar Prometheus + Node exporter + Grafana

Crear un panel en Grafana.

Seleccionar Prometheus como Data Source.

Ejemplos de métricas:

node_cpu_seconds_total
node_memory_MemAvailable_bytes
node_filesystem_avail_bytes

Visualizar CPU, RAM, disco y otros recursos de VM2.


<img width="1901" height="955" alt="image" src="https://github.com/user-attachments/assets/fdaa7ecd-acd2-4379-a334-5ba0ad23380a" />

<img width="1913" height="950" alt="image" src="https://github.com/user-attachments/assets/435e55ff-28ee-4597-be63-5e363b57e09d" />

# 4 Testear iPerf
1. Preparación del entorno

VM1: IP 192.168.1.56, funciona como servidor iPerf3

VM2: Cliente iPerf3

Logs y resultados guardados en /data/iperf/logs para no llenar el disco principal

sudo mkdir -p /data/iperf/logs
sudo chown $USER:$USER /data/iperf/logs
2. Instalación de iPerf3
sudo apt update
sudo apt install -y iperf3
iperf3 --version
3. Configuración VM1 como servidor
iperf3 -s -D --logfile /data/iperf/logs/iperf_server.log

-s → servidor

-D → daemon, corre en segundo plano

--logfile → guardar salida en archivo

4. Pruebas desde VM2 (cliente)
4.1 Prueba básica (1 flujo, 30 segundos)
iperf3 -c 192.168.1.56 -t 30 -P 1 --logfile /data/iperf/logs/iperf_client_1.log

Resultado relevante:

[  6]   0.00-30.00  sec  3.04 GBytes   871 Mbits/sec  1485             sender
[  6]   0.00-30.01  sec  3.04 GBytes   870 Mbits/sec                  receiver

Comentario: La conexión alcanza ~870 Mbits/s en un solo flujo, lo que indica un rendimiento muy estable entre VM1 y VM2.

4.2 Diferentes tamaños de paquetes (64K, 128K, 256K)
iperf3 -c 192.168.1.56 -l 64K  -t 30 -P 2 --logfile /data/iperf/logs/iperf_client_64K.log
iperf3 -c 192.168.1.56 -l 128K -t 30 -P 2 --logfile /data/iperf/logs/iperf_client_128K.log
iperf3 -c 192.168.1.56 -l 256K -t 30 -P 2 --logfile /data/iperf/logs/iperf_client_256K.log

Comentario: Probar distintos tamaños de paquete permite observar cómo cambia la eficiencia del enlace. Paquetes más grandes suelen reducir la sobrecarga del protocolo y aumentar la tasa efectiva.

4.3 Modo Reverse (servidor envía, cliente recibe)
iperf3 -c 192.168.1.56 -R -t 30 -P 2 --logfile /data/iperf/logs/iperf_client_reverse.log

Comentario: Útil para medir la velocidad de subida desde el servidor hacia el cliente. Comparando con la prueba estándar se puede ver la simetría del enlace.

4.4 Pruebas concurrentes (4 flujos simultáneos)
iperf3 -c 192.168.1.56 -P 4 -t 20 --logfile /data/iperf/logs/iperf_concurrent1.log &
iperf3 -c 192.168.1.56 -P 4 -t 20 --logfile /data/iperf/logs/iperf_concurrent2.log &

Comentario: Permite analizar la capacidad total del enlace con múltiples flujos. Útil para simular tráfico real de varias aplicaciones simultáneas.

4.5 Logs de resultados

Para extraer los datos más importantes:

grep "sender" /data/iperf/logs/*.log
grep "receiver" /data/iperf/logs/*.log

Interpretación:

sender → velocidad medida en el emisor (cliente o servidor según el modo)

receiver → velocidad medida en el receptor

Ejemplo de salida:

/data/iperf/logs/iperf_client_1.log:[  6]   0.00-30.00  sec  3.04 GBytes   871 Mbits/sec  1485             sender
/data/iperf/logs/iperf_client_1.log:[  6]   0.00-30.01  sec  3.04 GBytes   870 Mbits/sec                  receiver
5. Conclusiones

La velocidad promedio entre VM1 y VM2 es ~870 Mbits/s, estable y consistente.

Incrementar el número de flujos o el tamaño de los paquetes ayuda a aprovechar mejor el enlace.

El modo reverse permite verificar simetría y capacidad de subida.

Guardar logs en un disco secundario evita saturar el disco principal y facilita la revisión de resultados.
<img width="1916" height="943" alt="image" src="https://github.com/user-attachments/assets/88deb394-8c2a-4652-9983-e26db27c47a8" />

