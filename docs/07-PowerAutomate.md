# Microsoft Power Platform - Desarrollo de aplicaciones con Power Apps, Microsoft Lists y Power Automate

## Introducción

El objetivo de esta práctica ha sido desarrollar una serie de aplicaciones utilizando Microsoft Power Platform con el propósito de automatizar distintos procesos habituales dentro de una empresa. Para ello se han integrado tres de las herramientas más utilizadas dentro del ecosistema Microsoft 365: **Microsoft Lists**, **Power Apps** y **Power Automate**.

Aunque cada uno de los proyectos aborda una necesidad diferente, todos comparten la misma arquitectura de funcionamiento. En primer lugar, el usuario interactúa con una aplicación desarrollada en Power Apps. La información introducida se almacena automáticamente en una lista de Microsoft Lists, que actúa como base de datos. Finalmente, Power Automate monitoriza dicha información y ejecuta las automatizaciones necesarias según el estado o los cambios producidos en cada registro.

Esta forma de trabajar permite separar claramente cada una de las responsabilidades del sistema. Power Apps se encarga de ofrecer una interfaz sencilla para el usuario, Microsoft Lists almacena toda la información de forma estructurada y Power Automate automatiza aquellas tareas que anteriormente requerían intervención manual.

Los proyectos desarrollados han sido los siguientes:

* Gestor de incidencias informáticas.
* Gestión de solicitudes de vacaciones.
* Control de visitas a la empresa.

En los siguientes apartados se documenta el proceso completo de desarrollo de cada uno de ellos.

---

# Proyecto 1. Gestor de incidencias informáticas

## Objetivo del proyecto

El primer proyecto consiste en el desarrollo de una pequeña aplicación para gestionar incidencias informáticas.

Aunque existen numerosas soluciones profesionales para este tipo de sistemas (como Jira Service Management, ServiceNow o GLPI), el objetivo de esta práctica no es replicar todas sus funcionalidades, sino comprender cómo desarrollar una solución básica utilizando exclusivamente herramientas de Microsoft Power Platform.

En un entorno empresarial real un gestor de incidencias suele incorporar muchas más funcionalidades que las implementadas en esta práctica. Entre ellas pueden encontrarse la asignación automática de técnicos, clasificación por categorías, niveles de prioridad, tiempos máximos de resolución (SLA), seguimiento del tiempo invertido, historial de actuaciones, archivos adjuntos, comentarios internos, generación de informes o integración con sistemas de inventario.

Sin embargo, para comprender el funcionamiento de Power Apps y Power Automate resulta más adecuado comenzar con un proceso simplificado que permita entender cómo interactúan las distintas herramientas sin añadir una complejidad innecesaria.

El sistema desarrollado permite registrar incidencias, almacenarlas de forma estructurada, notificar automáticamente al personal técnico cuando aparece una nueva incidencia y avisar al usuario cuando esta queda resuelta.

---

# Análisis previo

Antes de desarrollar cualquier aplicación es recomendable realizar un pequeño análisis del proceso que se desea digitalizar.

Uno de los errores más habituales consiste en comenzar directamente a crear formularios o automatizaciones sin haber definido previamente qué información será necesaria almacenar y cómo va a circular esa información entre los distintos usuarios.

En este caso el proceso es relativamente sencillo.

Un usuario detecta un problema informático y accede a la aplicación para registrar una incidencia. Esa información debe almacenarse de forma estructurada para poder consultarla posteriormente y permitir que el técnico encargado pueda gestionarla.

Cuando aparece una nueva incidencia el técnico debe ser informado automáticamente para poder comenzar su resolución. Finalmente, una vez solucionado el problema, el usuario debe recibir una notificación indicando que la incidencia ha sido resuelta.

Este flujo puede representarse de forma resumida mediante el siguiente esquema:

```text
Usuario registra incidencia
            │
            ▼
      Power Apps
            │
            ▼
    Microsoft Lists
            │
            ▼
     Power Automate
            │
     ┌──────┴──────┐
     ▼             ▼
Aviso técnico   Aviso usuario
```

Aunque el proceso resulta sencillo, dedicar unos minutos a definir este flujo facilita enormemente el desarrollo posterior, ya que permite identificar qué información será necesaria en cada etapa y qué automatizaciones deberán implementarse posteriormente.

---

# Diseño de la base de datos

Una vez definido el flujo de trabajo, el siguiente paso consiste en diseñar la estructura donde se almacenará toda la información.

En Power Platform la forma más sencilla de hacerlo es mediante **Microsoft Lists**, ya que permite crear bases de datos de forma visual sin necesidad de utilizar gestores de bases de datos tradicionales.
<img width="2000" height="1136" alt="image" src="https://github.com/user-attachments/assets/c2066cdf-fd1f-486f-a48e-c9578454997e" />

Para crear una nueva lista basta con acceder a Microsoft Lists utilizando la cuenta corporativa o educativa y seleccionar la opción **Crear nueva lista**. A continuación la plataforma solicita un nombre para la lista y genera automáticamente una estructura vacía sobre la que posteriormente podrán añadirse todas las columnas necesarias.

Aunque crear una lista es un proceso muy rápido, probablemente sea una de las fases más importantes del desarrollo. Una estructura bien diseñada facilita enormemente el trabajo posterior en Power Apps y Power Automate, mientras que una mala elección de los campos puede obligar a modificar toda la aplicación cuando el desarrollo ya está avanzado.

Por este motivo, antes de añadir ninguna columna conviene responder a algunas preguntas:

* ¿Qué información necesita introducir realmente el usuario?
* ¿Qué información utilizarán posteriormente los técnicos?
* ¿Qué datos serán necesarios para automatizar el proceso?
* ¿Qué campos podrán utilizarse posteriormente para realizar búsquedas o filtros?

Responder previamente a estas cuestiones evita incorporar información innecesaria y ayuda a mantener una estructura sencilla y coherente.

---

# Selección de los tipos de datos

Además de decidir qué información almacenar, también es importante seleccionar correctamente el tipo de dato asociado a cada columna.

Esta decisión no solamente afecta a la forma en la que Microsoft Lists almacena la información, sino también al comportamiento que tendrán posteriormente Power Apps y Power Automate.

Los tipos de datos más utilizados durante este proyecto han sido los siguientes.

### Texto

Se utiliza cuando únicamente es necesario almacenar una cadena de caracteres sencilla, como puede ser un asunto o un nombre.

Este tipo de campo permite búsquedas rápidas y resulta adecuado para información breve.

### Texto de varias líneas

Cuando el usuario necesita escribir una descripción más extensa, como ocurre con el cuerpo de una incidencia, resulta más apropiado utilizar un campo de texto multilínea.

Esto mejora la experiencia del usuario durante la introducción de información y facilita posteriormente la lectura del contenido.

### Persona

Uno de los tipos de dato más interesantes que ofrece Microsoft Lists es el campo **Persona**.

En lugar de almacenar únicamente un nombre escrito manualmente, este tipo de columna se conecta directamente con los usuarios existentes dentro del entorno Microsoft 365.

Gracias a ello Power Apps puede mostrar automáticamente un selector de usuarios y Power Automate tiene acceso a información adicional como el nombre completo, el correo electrónico o el identificador del usuario.

Esta característica resulta especialmente útil cuando posteriormente se necesita enviar notificaciones por correo electrónico sin que el usuario tenga que escribir manualmente su dirección.

### Fecha y hora

Los campos de fecha permiten registrar momentos concretos dentro del proceso.

En este proyecto se utilizan para almacenar la fecha de creación de la incidencia y la fecha de resolución.

Además de facilitar consultas posteriores, permiten conocer el tiempo transcurrido entre la apertura y la resolución de cada incidencia.

### Elección

Los campos de tipo **Elección** permiten limitar las respuestas posibles a un conjunto previamente definido.

Su utilización resulta especialmente recomendable cuando únicamente existen unas pocas opciones válidas, ya que evita errores de escritura y mantiene la información homogénea en todos los registros.

En este proyecto se utilizaron para los niveles de urgencia y para el estado de la incidencia.

Elegir correctamente cada tipo de dato desde el principio reduce considerablemente la complejidad del desarrollo posterior y evita tener que realizar modificaciones cuando la aplicación ya está terminada.

---

# Creación de la lista

Una vez definida la estructura de la información, se procedió a crear la lista **FlujoIncidencias**.

Las columnas utilizadas fueron las siguientes:

| Columna          | Tipo                   |
| ---------------- | ---------------------- |
| Usuario          | Persona                |
| FechaEntrada     | Fecha y hora           |
| Urgencia         | Elección               |
| Asunto           | Texto                  |
| CuerpoIncidencia | Texto de varias líneas |
| FechaResolucion  | Fecha y hora           |
| CuerpoResolucion | Texto de varias líneas |
| EstadoIncidencia | Elección               |

Para el campo **Urgencia** se configuraron cuatro niveles:

* Baja
* Media
* Alta
* Crítica

Mientras que el campo **EstadoIncidencia** quedó definido con los siguientes valores:

* Pendiente
* En proceso
* Resuelta

Con esta estructura ya se dispone de una base de datos completamente funcional sobre la que desarrollar el resto del proyecto.



---

# Desarrollo de la aplicación con Power Apps

Una vez diseñada la base de datos en Microsoft Lists, el siguiente paso consiste en desarrollar la aplicación que utilizarán los usuarios para registrar las incidencias. Una de las principales ventajas de Power Apps es que permite generar una aplicación completamente funcional a partir de una lista ya existente, reduciendo enormemente el tiempo de desarrollo.

## Creación de la aplicación

Para comenzar, se accede al portal de **Power Apps** utilizando la misma cuenta de Microsoft 365 con la que se creó previamente la lista.

Desde la pantalla principal se selecciona la opción **Crear** y, dentro de las distintas alternativas disponibles, se elige **Comenzar con datos**. Esta opción permite crear una aplicación utilizando como origen una base de datos ya existente.

En la siguiente ventana Power Apps solicita seleccionar el origen de datos. En este caso se selecciona **SharePoint**, ya que Microsoft Lists utiliza internamente esta tecnología para almacenar toda la información.

El asistente solicita la dirección del sitio de SharePoint donde se encuentra alojada la lista. Es importante indicar únicamente la dirección del sitio y **no la URL completa de la lista**.

Por ejemplo, si la dirección de la lista fuese:

```text
https://empresa-my.sharepoint.com/personal/usuario_empresa_com/Lists/FlujoIncidencias
```

la dirección que debe introducirse en Power Apps sería únicamente:

```text
https://empresa-my.sharepoint.com/personal/usuario_empresa_com
```

Una vez establecida la conexión, Power Apps consulta automáticamente todas las listas disponibles dentro de ese sitio de SharePoint y muestra un listado con ellas.

Únicamente hay que seleccionar la lista **FlujoIncidencias** y pulsar **Conectar**.

A partir de ese momento Power Apps comienza a generar automáticamente la aplicación.

<img width="2000" height="1125" alt="image" src="https://github.com/user-attachments/assets/64907f4e-d6f3-4351-885a-a43f67cfd394" />


---

## Generación automática de la interfaz

Tras analizar la estructura de la lista, Power Apps crea prácticamente toda la aplicación de forma automática.

La herramienta interpreta el tipo de cada columna y selecciona el control visual más adecuado para representar cada dato.

Por ejemplo:

* Los campos de texto aparecen como cajas de texto.
* Los campos de varias líneas generan cuadros de escritura más amplios.
* Los campos de tipo **Persona** muestran un selector conectado directamente al directorio de Microsoft 365.
* Los campos de tipo **Elección** aparecen como listas desplegables con todas las opciones definidas previamente en Microsoft Lists.
* Los campos de fecha utilizan automáticamente un selector de calendario.

Gracias a ello no es necesario programar manualmente la mayor parte de la interfaz, ya que Power Apps adapta automáticamente el formulario a la estructura de la lista.

Además del formulario, la aplicación incorpora de forma automática una pantalla principal con un listado de todas las incidencias registradas y otra pantalla destinada a visualizar el detalle de cada una de ellas.

En pocos segundos se obtiene una aplicación completamente funcional capaz de crear, consultar y modificar registros almacenados en Microsoft Lists.

---

## Organización del proyecto

Durante el desarrollo, Power Apps organiza todos los elementos de la aplicación mediante una estructura en forma de árbol situada en el panel izquierdo de la pantalla.

En dicho árbol aparecen todas las pantallas de la aplicación junto con cada uno de los controles que contiene.

Normalmente encontraremos una estructura similar a la siguiente:

```text
App

├── BrowseScreen
│     ├── Gallery
│     ├── Botón Nuevo
│     └── Barra de búsqueda
│
├── DetailScreen
│     ├── Formulario de detalle
│     └── Botones de navegación
│
└── EditScreen
      ├── EditForm
      ├── Botón Guardar
      └── Botón Cancelar
```

Esta organización facilita localizar rápidamente cualquier componente de la aplicación y permite modificarlo sin necesidad de buscarlo directamente sobre la interfaz gráfica.

---

## Personalización de la aplicación

Aunque la aplicación generada automáticamente es completamente funcional, normalmente será necesario realizar pequeñas modificaciones para adaptarla a las necesidades del proyecto.

Todas estas personalizaciones pueden realizarse desde el panel izquierdo seleccionando el control correspondiente.

Entre las modificaciones más habituales se encuentran:

* Cambiar el nombre de las pantallas.
* Modificar los títulos mostrados al usuario.
* Cambiar el orden de los campos del formulario.
* Ocultar columnas que no deban editarse manualmente.
* Añadir nuevos botones de navegación.
* Cambiar textos, tamaños y posiciones de los controles.
* Modificar colores o aspectos visuales de la aplicación.

La mayoría de estos cambios pueden realizarse sin escribir código, utilizando únicamente el panel de propiedades situado en la parte derecha del editor.

Cuando es necesario incorporar alguna funcionalidad adicional, Power Apps permite configurar el comportamiento de cada control mediante expresiones escritas en la barra de fórmulas situada en la parte superior de la ventana.

<img width="1940" height="923" alt="image" src="https://github.com/user-attachments/assets/37d10937-596a-4272-b553-4a7363513a48" />

---

## Aplicación completamente funcional

Una vez conectada la lista y realizadas las pequeñas personalizaciones necesarias, la aplicación queda completamente operativa.

Cada vez que un usuario rellena el formulario y pulsa el botón **Guardar**, Power Apps envía automáticamente toda la información a Microsoft Lists.

Del mismo modo, cualquier modificación realizada sobre un registro queda reflejada inmediatamente en la lista sin necesidad de realizar sincronizaciones manuales.

En este punto puede considerarse finalizado el desarrollo de la aplicación. La siguiente fase del proyecto consiste en incorporar la automatización mediante Power Automate, encargándose de enviar las notificaciones correspondientes cuando se produzcan los distintos eventos definidos durante el diseño del sistema.

---

# Automatización del proceso con Power Automate

Una vez finalizado el desarrollo de la aplicación, el siguiente paso consistió en automatizar aquellas tareas que hasta ese momento seguirían dependiendo de la intervención manual de los usuarios.

Uno de los principales objetivos de Power Automate es precisamente eliminar este tipo de tareas repetitivas, permitiendo que determinadas acciones se ejecuten automáticamente cuando ocurre un evento concreto dentro del sistema.

En este proyecto se implementaron dos automatizaciones diferentes.

La primera de ellas se encarga de informar al técnico responsable cada vez que se registra una nueva incidencia, mientras que la segunda notifica al usuario cuando dicha incidencia ha sido resuelta.

De esta forma, la comunicación entre ambas partes se produce de manera completamente automática, sin necesidad de revisar continuamente la lista de SharePoint.

---

# Flujo 1. Notificación automática al técnico

## Objetivo

El objetivo de este flujo es reducir el tiempo que transcurre entre la creación de una incidencia y el momento en el que el técnico tiene conocimiento de ella.

En un proceso manual, el técnico tendría que acceder periódicamente a la lista de incidencias para comprobar si existen nuevas solicitudes pendientes de atender. A medida que aumenta el número de usuarios, este sistema deja de ser eficiente y resulta fácil que alguna incidencia pase desapercibida.

Mediante Power Automate es posible evitar esta situación haciendo que sea el propio sistema quien notifique automáticamente la existencia de una nueva incidencia en el mismo instante en el que esta queda registrada.

---

## Creación del flujo

Desde el portal de Power Automate se seleccionó la opción **Crear** y posteriormente **Flujo automatizado**.

Durante la creación del flujo es necesario elegir un desencadenador, es decir, el evento que provocará el inicio de la automatización.

En este caso se seleccionó el desencadenador:

```text
Cuando se crea un elemento
```

Este desencadenador resulta el más adecuado porque únicamente debe ejecutarse cuando un usuario registra una incidencia nueva.

Una vez seleccionado, se tiene que configrurar la conexión indicando el sitio de SharePoint, que es el mismo que se ha utilizado con power apps y la lista **FlujoIncidencias**, de manera que Power Automate pudiera supervisar automáticamente cualquier nuevo registro que apareciese en dicha lista.

A partir de ese momento, cada nueva incidencia registrada desde Power Apps activa inmediatamente el flujo.

<img width="1962" height="1125" alt="image" src="https://github.com/user-attachments/assets/02d629fd-0429-4ab7-8d8e-5048349f1ce4" />


---

## Envío de la notificación

Tras el desencadenador se añadió la acción:

```text
Enviar correo electrónico (V2)
```

Esta acción permite generar un correo electrónico completamente dinámico utilizando la información recibida desde el propio desencadenador.

En lugar de escribir manualmente el contenido del mensaje, Power Automate ofrece el denominado **contenido dinámico**, que permite insertar automáticamente valores procedentes del elemento que acaba de crearse.

Gracias a ello cada correo contiene exactamente la información correspondiente a la incidencia registrada.

En este proyecto se incorporaron los siguientes datos:

* Usuario que ha registrado la incidencia.
* Fecha de creación.
* Nivel de urgencia.
* Asunto.
* Descripción de la incidencia.

El destinatario del mensaje corresponde al técnico responsable del soporte informático.

Como resultado, cada vez que un usuario crea una incidencia desde la aplicación, el técnico recibe inmediatamente un correo electrónico con toda la información necesaria para comenzar su resolución.

Este proceso se ejecuta de forma completamente automática y no requiere ninguna intervención adicional.

---

# Flujo 2. Notificación al usuario tras la resolución

## Objetivo

Una vez registrada una incidencia comienza el trabajo del técnico.

Cuando este consigue resolver el problema, resulta conveniente informar automáticamente al usuario para evitar que tenga que consultar continuamente el estado de su solicitud.

El segundo flujo desarrollado tiene precisamente esta finalidad.

Su misión consiste en detectar cuándo una incidencia pasa al estado **Resuelta** y comunicar automáticamente dicha información al usuario.

---

## Configuración del desencadenador

Al contrario que el flujo anterior, en este caso no interesa detectar la creación de nuevos elementos, sino las modificaciones que se producen sobre ellos.

Por ese motivo el flujo utiliza el desencadenador:

```text
Cuando se modifica un elemento
```

Este desencadenador permite supervisar cualquier cambio realizado sobre los registros de la lista.

Sin embargo, no todas las modificaciones deben generar una notificación.

Para evitar envíos innecesarios se añadió inmediatamente una acción **Condición**.

---

## Evaluación del estado

La condición comprueba el contenido del campo **EstadoIncidencia**.

La comparación utilizada fue la siguiente:

```text
EstadoIncidencia

es igual que

Resuelta
```

Esta comprobación garantiza que el correo únicamente será enviado cuando la incidencia haya finalizado realmente.

Si el técnico modifica cualquier otro dato de la incidencia sin cambiar su estado, el flujo finalizará sin realizar ninguna acción adicional.

---

## Envío del correo al usuario

Cuando la condición se cumple, Power Automate ejecuta la rama afirmativa del flujo.

En esta rama se añadió nuevamente la acción:

```text
Enviar correo electrónico (V2)
```

En este caso el destinatario ya no es el técnico, sino el propio usuario que registró la incidencia.

Gracias al uso del campo de tipo **Persona** creado previamente en Microsoft Lists, Power Automate obtiene automáticamente la dirección de correo electrónico del usuario utilizando el contenido dinámico, sin necesidad de almacenarla manualmente en la aplicación.

El correo informa de que la incidencia ha sido resuelta e incorpora información adicional procedente de la lista, como:

* Asunto de la incidencia.
* Fecha de resolución.
* Comentarios o explicación introducida por el técnico.

De este modo el usuario recibe toda la información necesaria sin tener que acceder nuevamente a la aplicación.

---

# Funcionamiento conjunto de ambos flujos

Aunque los dos flujos son independientes, ambos trabajan sobre la misma lista de Microsoft Lists y forman parte del mismo proceso de gestión.

El primero actúa en el momento en que una incidencia entra en el sistema, notificando al personal técnico.

El segundo interviene cuando la incidencia finaliza, comunicando el resultado al usuario.

Entre ambos automatizan completamente el proceso de comunicación, eliminando la necesidad de realizar avisos manuales y garantizando que todas las incidencias sigan el mismo ciclo de funcionamiento.

---

# Resultado obtenido

El resultado final es una aplicación plenamente funcional para la gestión de incidencias informáticas.

El usuario únicamente necesita acceder a Power Apps, rellenar el formulario y guardar la incidencia.

A partir de ese momento, el resto del proceso se desarrolla de forma automática.

La incidencia queda almacenada en Microsoft Lists, el técnico recibe un aviso inmediato para comenzar su gestión y, una vez resuelta, el usuario recibe una notificación indicando que su solicitud ha sido atendida.

La integración entre Power Apps, Microsoft Lists y Power Automate permite así construir un sistema sencillo pero perfectamente operativo, demostrando cómo es posible automatizar procesos habituales de una empresa utilizando herramientas de bajo código.

---

# Conclusiones del proyecto

Este primer proyecto ha servido para comprender el funcionamiento conjunto de las principales herramientas que forman parte de Microsoft Power Platform.

A pesar de tratarse de una aplicación sencilla, durante su desarrollo se han trabajado conceptos fundamentales como el diseño previo de una base de datos, la selección de los tipos de datos más adecuados, la creación de formularios mediante Power Apps y la automatización de procesos utilizando Power Automate.

Además, el proyecto pone de manifiesto la importancia de diseñar correctamente el flujo de información antes de comenzar el desarrollo. Una buena planificación inicial facilita enormemente la creación de la aplicación y reduce la necesidad de realizar modificaciones posteriores.

Aunque el gestor desarrollado constituye una versión simplificada de un sistema de gestión de incidencias profesional, la estructura implementada puede ampliarse fácilmente incorporando nuevas funcionalidades, como asignación automática de técnicos, prioridades dinámicas, tiempos máximos de resolución, adjuntos, comentarios internos o paneles de seguimiento, manteniendo la misma arquitectura utilizada durante esta práctica.


---

# Proyecto 2. Gestión de solicitudes de vacaciones

## Descripción

El segundo proyecto tiene como objetivo automatizar el proceso de solicitud y aprobación de vacaciones dentro de una empresa.

La aplicación permite que cualquier empleado pueda registrar una solicitud indicando el período de vacaciones y el motivo correspondiente. Una vez almacenada la información en Microsoft Lists, Power Automate inicia automáticamente un proceso de aprobación dirigido al supervisor mediante Microsoft Teams.

Según la decisión tomada por el responsable, el sistema actualiza automáticamente el estado de la solicitud y comunica el resultado al empleado mediante correo electrónico.

El funcionamiento general del proyecto puede resumirse del siguiente modo:

```text
Empleado

↓

Power Apps

↓

Microsoft Lists

↓

Power Automate

↓

Solicitud de aprobación (Teams)

↓

Supervisor

↓

Actualizar estado

↓

Correo al empleado
```

---

# Creación de la lista de SharePoint

Para este proyecto se creó una nueva lista denominada **ListaVacas**.

En ella se almacenan todas las solicitudes realizadas por los empleados.

Las columnas utilizadas fueron las siguientes:

| Columna     | Tipo                   |
| ----------- | ---------------------- |
| Empleado    | Persona                |
| FechaInicio | Fecha                  |
| FechaFin    | Fecha                  |
| Motivo      | Texto de varias líneas |
| Estado      | Elección               |

La columna **Estado** contiene tres posibles valores:

* Pendiente
* Aceptada
* Rechazada

Cada nueva solicitud queda registrada inicialmente con el estado **Pendiente**, indicando que todavía está pendiente de revisión por parte del supervisor.

---

# Desarrollo de la aplicación en Power Apps

Al igual que en el proyecto anterior, la aplicación se desarrolló utilizando Microsoft Lists como origen de datos.

Los pasos realizados fueron los siguientes:

1. Crear una nueva aplicación de lienzo.
2. Seleccionar **Microsoft Lists** como origen de datos.
3. Conectar la lista **ListaVacas**.
4. Permitir que Power Apps generase automáticamente la estructura inicial de la aplicación.

La aplicación quedó compuesta por varias pantallas que permiten registrar nuevas solicitudes y consultar todas las peticiones almacenadas.

El formulario solicita al usuario la siguiente información:

* Empleado.
* Fecha de inicio.
* Fecha de fin.
* Motivo.

El campo **Estado** no es editable por el usuario, ya que será actualizado automáticamente por Power Automate cuando el supervisor tome una decisión.

---

# Automatización mediante Power Automate

En este proyecto la automatización tiene un papel mucho más importante que en el anterior, ya que interviene durante todo el proceso de aprobación.

Para ello se desarrolló un flujo compuesto por varias acciones encadenadas.

---

# Inicio del flujo

El flujo comienza automáticamente cada vez que un empleado registra una nueva solicitud.

Para ello se creó un flujo automatizado utilizando el desencadenador:

```text
Cuando se crea un elemento
```

Como origen de datos se seleccionó la lista **ListaVacas**.

Cada nuevo registro activa automáticamente el flujo sin necesidad de ninguna intervención adicional.

---

# Solicitud de aprobación mediante Microsoft Teams

Una vez iniciada la automatización, el siguiente paso consiste en solicitar la aprobación del responsable.

Para ello se añadió la acción:

```text
Iniciar y esperar una aprobación
```

El tipo de aprobación seleccionado fue:

```text
Aprobar o rechazar - Primera respuesta
```

En esta acción se configuraron los siguientes parámetros:

**Asignado a**

Supervisor responsable de aprobar las vacaciones.

**Título**

Solicitud de vacaciones.

**Detalles**

Se incluyeron mediante contenido dinámico los datos introducidos por el empleado:

* Nombre del empleado.
* Fecha de inicio.
* Fecha de fin.
* Motivo de la solicitud.

Power Automate envía automáticamente esta solicitud al supervisor mediante Microsoft Teams, donde puede aprobarla o rechazarla directamente.

Mientras no exista una respuesta, el flujo permanece esperando.

---

# Evaluación de la respuesta

Una vez que el supervisor responde, el flujo continúa su ejecución.

Para ello se añadió una acción **Condición**, cuya finalidad es comprobar el resultado obtenido durante la aprobación.

La condición utilizada fue la siguiente:

```text
Outcome

es igual que

Approve
```

Si la respuesta recibida es **Approve**, se ejecutará la rama **Sí**.

En cualquier otro caso se ejecutará automáticamente la rama **No**.

---

# Rama de aprobación

Cuando el supervisor aprueba la solicitud, el flujo realiza dos acciones consecutivas.

## Envío del correo electrónico

En primer lugar se añade una acción:

```text
Enviar correo electrónico (V2)
```

El destinatario corresponde al empleado que realizó la solicitud.

El mensaje informa de que la petición ha sido aprobada por el responsable y que el estado de la solicitud ha sido actualizado.

---

## Actualización automática del estado

Después del envío del correo se utiliza la acción:

```text
Actualizar elemento
```

Esta acción modifica el registro almacenado en Microsoft Lists utilizando el identificador del elemento recibido desde el desencadenador.

El campo actualizado es:

```text
Estado = Aceptada
```

De este modo la lista refleja automáticamente el resultado de la aprobación.

---

# Rama de rechazo

Si el supervisor decide rechazar la solicitud, el funcionamiento es exactamente el mismo, modificando únicamente el contenido del correo y el estado almacenado.

En primer lugar se envía un correo electrónico notificando el rechazo de la petición.

Posteriormente se ejecuta nuevamente la acción **Actualizar elemento**, estableciendo:

```text
Estado = Rechazada
```

Con ello la información almacenada en Microsoft Lists permanece sincronizada con la decisión tomada por el supervisor.

---

# Resultado obtenido

La integración entre Power Apps, Microsoft Lists, Microsoft Teams y Power Automate permite automatizar completamente el proceso de gestión de vacaciones.

El empleado únicamente necesita registrar la solicitud desde la aplicación.

A partir de ese momento, el resto del proceso se realiza automáticamente:

* Se envía la solicitud al supervisor.
* El supervisor responde desde Teams.
* El empleado recibe el resultado por correo electrónico.
* El estado de la solicitud se actualiza automáticamente en la lista de SharePoint.

Gracias a esta automatización desaparece la necesidad de gestionar manualmente las solicitudes, consiguiendo un proceso más rápido, organizado y fácilmente consultable desde la propia aplicación.

---

# Proyecto 3. Control de visitas a la empresa

## Descripción

El tercer proyecto consiste en desarrollar una aplicación para gestionar el acceso de visitantes a una empresa.

El objetivo es sustituir el tradicional registro en papel por una aplicación que permita almacenar toda la información de las visitas en formato digital y automatizar determinadas tareas, como el envío de un correo de bienvenida o el cierre automático de las visitas al finalizar la jornada laboral.

Cada visita queda registrada desde una aplicación desarrollada en Power Apps, mientras que Microsoft Lists almacena toda la información y Power Automate se encarga de ejecutar las automatizaciones configuradas.

El funcionamiento general del proyecto puede resumirse de la siguiente forma:

```text
Visitante

↓

Power Apps

↓

Microsoft Lists

↓

Power Automate

├── Envío de correo de bienvenida
└── Cierre automático de la visita
```

---

# Creación de la lista de SharePoint

Se creó una nueva lista denominada **VisitasEmpresa**, destinada a almacenar toda la información relativa a las visitas realizadas.

La lista contiene las siguientes columnas:

| Columna        | Tipo         |
| -------------- | ------------ |
| Nombre         | Texto        |
| Empresa        | Texto        |
| EmailVisitante | Texto        |
| PersonaVisitar | Texto        |
| HoraEntrada    | Fecha y hora |
| HoraSalida     | Fecha y hora |
| EstadoVisita   | Elección     |

La columna **EstadoVisita** dispone de los siguientes valores:

* Abierta
* Finalizada

Cada nueva visita queda registrada inicialmente con el estado **Abierta**, indicando que el visitante continúa dentro de las instalaciones.

---

# Desarrollo de la aplicación en Power Apps

Una vez creada la lista se desarrolló la aplicación conectándola directamente con Microsoft Lists.

Al igual que en los proyectos anteriores, Power Apps generó automáticamente la estructura básica de la aplicación, compuesta por una pantalla de consulta, una pantalla de detalle y un formulario para registrar nuevas visitas.

Posteriormente se realizaron varias modificaciones para adaptar el funcionamiento a los requisitos de la práctica.

---

# Registro de una nueva visita

El formulario solicita al usuario únicamente la información necesaria para identificar la visita.

Los campos visibles son:

* Nombre del visitante.
* Empresa de procedencia.
* Correo electrónico.
* Persona a la que visita.

Los campos **HoraEntrada**, **HoraSalida** y **EstadoVisita** permanecen ocultos al usuario, ya que son gestionados automáticamente por la propia aplicación.

Cuando el usuario pulsa el botón **Guardar**, Power Apps registra automáticamente la fecha y hora actual utilizando la función:

```PowerApps
Now()
```

Este valor se almacena en el campo **HoraEntrada**, evitando que el usuario tenga que introducir manualmente la hora de acceso.

De igual forma, el estado inicial de la visita queda establecido automáticamente como **Abierta**.

Con esta configuración se garantiza que todas las visitas se registren siguiendo el mismo criterio y sin depender de la introducción manual de datos.

---

# Consulta del histórico

Además del formulario de registro, la aplicación incorpora una pantalla destinada a consultar todas las visitas almacenadas.

Desde esta pantalla es posible visualizar, entre otros, los siguientes datos:

* Nombre del visitante.
* Empresa.
* Persona visitada.
* Hora de entrada.
* Hora de salida.
* Estado de la visita.

Esta funcionalidad permite consultar fácilmente el historial de accesos registrados en la empresa.

---

# Finalización manual de una visita

Uno de los requisitos del proyecto consistía en incorporar un botón que permitiera finalizar una visita cuando el visitante abandonase las instalaciones.

Para ello se añadió un botón denominado **Finalizar visita**.

Al pulsarlo se ejecuta una actualización sobre el registro seleccionado que realiza dos acciones simultáneamente:

* Registrar la fecha y hora actual como **HoraSalida**.
* Cambiar el estado de la visita a **Finalizada**.

De esta manera la aplicación mantiene actualizada la información de cada visitante sin necesidad de editar manualmente los registros almacenados en SharePoint.

---

# Automatización mediante Power Automate

Una vez finalizada la aplicación se desarrollaron dos flujos automáticos destinados a completar el funcionamiento del sistema.

---

# Flujo 1. Correo de bienvenida

## Objetivo

El objetivo de este flujo es confirmar al visitante que su registro se ha realizado correctamente.

Cada vez que se registra una nueva visita, Power Automate envía automáticamente un correo electrónico utilizando la dirección facilitada durante el registro.

---

## Creación del flujo

Se creó un nuevo flujo automatizado utilizando el desencadenador:

```text
Cuando se crea un elemento
```

Como origen de datos se seleccionó la lista **VisitasEmpresa**.

Cada nuevo registro inicia automáticamente la ejecución del flujo.

---

## Configuración del correo

Tras el desencadenador se añadió la acción:

```text
Enviar correo electrónico (V2)
```

El destinatario corresponde al campo **EmailVisitante** almacenado en la lista.

El cuerpo del mensaje incluye información como:

* Nombre del visitante.
* Empresa.
* Persona a visitar.
* Hora de entrada registrada.

Con este correo el visitante recibe una confirmación inmediata de que su acceso ha quedado correctamente registrado.

---

# Flujo 2. Cierre automático de visitas

## Objetivo

Aunque la aplicación permite finalizar manualmente una visita mediante el botón correspondiente, también se implementó un sistema que evita que puedan quedar visitas abiertas de forma indefinida.

Para ello se desarrolló un flujo programado que revisa diariamente todas las visitas registradas.

---

## Creación del flujo

En Power Automate se seleccionó la opción:

```text
Crear

↓

Flujo programado
```

El flujo se configuró para ejecutarse una vez al día al finalizar la jornada laboral.

En este proyecto se configuró una ejecución diaria a las **20:00 horas**, aunque este horario puede modificarse fácilmente según las necesidades de la empresa.

---

## Obtención de las visitas abiertas

La primera acción del flujo consiste en recuperar todas las visitas cuyo estado siga siendo **Abierta**.

Para ello se utilizó la acción:

```text
Obtener elementos
```

aplicando un filtro sobre el campo **EstadoVisita**.

De esta forma únicamente se procesan aquellas visitas que todavía no han sido cerradas manualmente.

---

## Actualización de las visitas

A continuación, mediante un bucle **Aplicar a cada**, el flujo recorre todas las visitas obtenidas y actualiza cada registro.

Durante esta actualización se realizan dos modificaciones:

* El campo **HoraSalida** recibe la fecha y hora actuales.
* El campo **EstadoVisita** pasa automáticamente al valor **Finalizada**.

Gracias a esta automatización ninguna visita permanece abierta más allá del horario definido.

---

# Resultado obtenido

Con este proyecto se ha conseguido digitalizar completamente el control de acceso de visitantes.

La aplicación permite registrar nuevas visitas de forma rápida, consultar el histórico de accesos y mantener actualizada la información tanto de la hora de entrada como de la hora de salida.

Además, la integración con Power Automate elimina tareas repetitivas mediante el envío automático de correos electrónicos y el cierre programado de las visitas que permanezcan abiertas al finalizar la jornada laboral.

---

# Conclusiones

Los tres proyectos desarrollados durante la práctica demuestran las posibilidades que ofrece Microsoft Power Platform para desarrollar aplicaciones empresariales sin necesidad de recurrir a programación tradicional.

La combinación de **Power Apps**, **Microsoft Lists** y **Power Automate** permite construir soluciones completas en las que la introducción de datos, el almacenamiento de la información y la automatización de procesos trabajan de forma integrada.

Durante el desarrollo de las aplicaciones se han implementado procesos habituales en cualquier organización, como la gestión de incidencias, la aprobación de solicitudes de vacaciones y el control de visitantes, automatizando tareas que normalmente requerirían intervención manual.

El resultado final es un conjunto de aplicaciones funcionales capaces de centralizar la información, reducir errores derivados de la gestión manual y mejorar la comunicación entre los distintos usuarios del sistema mediante notificaciones y flujos automáticos.

