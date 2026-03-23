# 1 Insltación

Es un paso sencillo, hay que ir a panel de control y una vez dentro tenemos que pinchar en activar o desactivar caracteristi as de windows, el siguiente paso es clicar en Hyper-V y también en espacio aislado de Windows, para crear un Sandbox.

<img width="1235" height="1019" alt="image" src="https://github.com/user-attachments/assets/bb3bcd63-b866-4613-90cd-e821f315bd04" />

# 2 Instalación de máquina y SO

La instalación del sistema operativo en máquinas virtuales se ha realizado utilizando Hyper-V con máquinas de Generación 1, lo que garantiza compatibilidad total con imágenes ISO sin depender de UEFI. En primer lugar, se crea una nueva máquina virtual desde el asistente de Hyper-V, asignándole un nombre identificativo y configurando una memoria inicial de entre 2048 MB y 3072 MB en función de los recursos disponibles del host. Se selecciona un disco duro virtual en formato VHDX con expansión dinámica, con un tamaño recomendado de al menos 40 GB, y se conecta la máquina a un switch virtual para dotarla de conectividad de red.

A continuación, se selecciona como fuente de instalación una imagen ISO del sistema operativo Windows Server 2022 previamente descargada y almacenada en el sistema local. Es importante asegurarse de que la unidad de DVD virtual esté correctamente configurada y que el orden de arranque priorice dicha unidad, de modo que la máquina virtual inicie directamente el instalador. Una vez arrancada, el sistema solicita la selección de idioma, formato regional y tipo de instalación, debiendo elegirse la edición con “Desktop Experience” para disponer de interfaz gráfica completa.


Durante el proceso de instalación, se selecciona el disco virtual creado previamente como destino, permitiendo que el instalador genere automáticamente las particiones necesarias. Tras la copia de archivos y reinicios automáticos, se finaliza la configuración inicial estableciendo la contraseña del usuario administrador. Una vez completada la instalación, la máquina virtual queda operativa con integración completa con el host, permitiendo funcionalidades como portapapeles bidireccional y mejor rendimiento general gracias a los servicios de integración incluidos.

