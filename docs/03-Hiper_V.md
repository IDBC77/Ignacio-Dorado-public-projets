# 1 Instalación

Es un paso sencillo, hay que ir a panel de control y una vez dentro tenemos que pinchar en activar o desactivar caracteristi as de windows, el siguiente paso es clicar en Hyper-V y también en espacio aislado de Windows, para crear un Sandbox.

<img width="1235" height="1019" alt="image" src="https://github.com/user-attachments/assets/bb3bcd63-b866-4613-90cd-e821f315bd04" />

# 2 Instalación de máquina y SO

La instalación del sistema operativo en máquinas virtuales se ha realizado utilizando Hyper-V con máquinas de Generación 1, lo que garantiza compatibilidad total con imágenes ISO sin depender de UEFI. En primer lugar, se crea una nueva máquina virtual desde el asistente de Hyper-V, asignándole un nombre identificativo y configurando una memoria inicial de entre 3072 MB en función de los recursos disponibles del host. Se selecciona un disco duro virtual en formato VHDX con expansión dinámica, con un tamaño recomendado de al menos 40 GB, y se conecta la máquina a un switch virtual para dotarla de conectividad de red.

A continuación, se selecciona como fuente de instalación una imagen ISO del sistema operativo Windows Server 2022 previamente descargada y almacenada en el sistema local. Es importante asegurarse de que la unidad de DVD virtual esté correctamente configurada y que el orden de arranque priorice dicha unidad, de modo que la máquina virtual inicie directamente el instalador. Una vez arrancada, el sistema solicita la selección de idioma, formato regional y tipo de instalación, debiendo elegirse la edición con “Desktop Experience” para disponer de interfaz gráfica completa.


Durante el proceso de instalación, se selecciona el disco virtual creado previamente como destino, permitiendo que el instalador genere automáticamente las particiones necesarias. Tras la copia de archivos y reinicios automáticos, se finaliza la configuración inicial estableciendo la contraseña del usuario administrador. Una vez completada la instalación, la máquina virtual queda operativa con integración completa con el host, permitiendo funcionalidades como portapapeles bidireccional y mejor rendimiento general gracias a los servicios de integración incluidos.

# 3 Servicio iSCSI

La implementación del almacenamiento se ha realizado mediante la configuración de un servidor iSCSI en una máquina virtual con Windows Server 2022, con el objetivo de simular una “cabina de discos” accesible a través de red. En primer lugar, se instala el rol Servidor de destino iSCSI desde el Administrador del servidor, dentro de los servicios de archivo y almacenamiento. Una vez instalado, se accede a la sección iSCSI y se crea un nuevo disco virtual mediante el asistente correspondiente, seleccionando un volumen existente del sistema C:\ como ubicación, definiendo un tamaño adecuado y utilizando formato VHDX con expansión dinámica. Durante este proceso, se crea también un destino iSCSI asociado, que actuará como punto de acceso al almacenamiento, permitiendo la conexión de clientes mediante dirección IP o identificador IQN.

A continuación, desde la máquina virtual cliente, se establece la conexión al almacenamiento remoto utilizando la herramienta Iniciador iSCSI. Se configura el descubrimiento del servidor introduciendo la dirección IP del equipo que actúa como servidor iSCSI, tras lo cual el destino previamente creado aparece disponible para su conexión. Una vez conectado, el sistema cliente reconoce automáticamente el disco remoto como un nuevo dispositivo de almacenamiento, aunque inicialmente aparece en estado desconocido y desactivado, lo cual es un comportamiento esperado en este tipo de configuraciones.

Finalmente, se procede a la preparación del disco desde la herramienta de administración de discos del sistema cliente. En primer lugar, el disco se activa manualmente (pasándolo a estado “En línea”, es la amnera de hacerlo siendo cliente), lo que permite posteriormente su inicialización, recomendándose el uso del estilo de partición GPT. Una vez inicializado, se crea un nuevo volumen simple ocupando el espacio disponible, asignándole una letra de unidad y formateándolo en NTFS. De este modo, el almacenamiento iSCSI queda completamente operativo y puede utilizarse como repositorio para máquinas virtuales, discos VHDX u otros recursos, reproduciendo un escenario real de almacenamiento centralizado en entornos profesionales.

<img width="1613" height="797" alt="Captura de pantalla 2026-03-23 125138" src="https://github.com/user-attachments/assets/7ceb874b-ac2b-40b4-a8a6-9434f200bfd4" />
