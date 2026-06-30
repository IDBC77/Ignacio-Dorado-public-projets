# Microsoft Power Platform - Desarrollo de aplicaciones con Power Apps, Microsoft Lists y Power Automate

## Introducción

El objetivo de esta práctica ha sido desarrollar varias aplicaciones utilizando Microsoft Power Platform para automatizar distintos procesos habituales dentro de una empresa. En todos los casos se ha utilizado la misma arquitectura de trabajo, combinando una aplicación desarrollada con Power Apps, una lista de Microsoft Lists como base de datos y uno o varios flujos de Power Automate para automatizar tareas que normalmente deberían realizarse de forma manual.

Las aplicaciones desarrolladas han sido las siguientes:

* Gestor de incidencias informáticas.
* Gestión de solicitudes de vacaciones.
* Control de visitas a la empresa.

Cada uno de estos proyectos es independiente, aunque todos comparten la misma filosofía de funcionamiento: recoger información mediante un formulario, almacenarla en SharePoint y automatizar acciones según el estado de los registros.

---

# Proyecto 1. Gestor de incidencias informáticas

## Descripción

El primer proyecto consiste en el desarrollo de una aplicación para gestionar incidencias informáticas de una empresa.

La finalidad es que cualquier usuario pueda registrar una incidencia desde una interfaz sencilla sin necesidad de acceder directamente a SharePoint. Una vez registrada, la incidencia queda almacenada automáticamente en una lista de Microsoft Lists y, a partir de ese momento, Power Automate se encarga de notificar al personal técnico y de informar posteriormente al usuario cuando la incidencia haya sido resuelta.

El proceso completo puede resumirse de la siguiente forma:

```
Usuario
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
     ├── Correo al técnico
     └── Correo al usuario cuando la incidencia se resuelve
```

---

# Creación de la lista de SharePoint

El primer paso consistió en crear una lista nueva desde Microsoft Lists.

Una vez creada, se añadieron las columnas necesarias para almacenar toda la información relacionada con cada incidencia.

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

En la columna **Urgencia** se configuraron los siguientes valores:

* Baja
* Media
* Alta
* Crítica

Mientras que en la columna **EstadoIncidencia** se establecieron los estados habituales de una incidencia:

* Pendiente
* En proceso
* Resuelta

Con esta lista ya se dispone de una base de datos donde almacenar todas las incidencias registradas por los usuarios.

---

# Desarrollo de la aplicación en Power Apps

Una vez preparada la lista, se creó la aplicación desde Power Apps utilizando como origen de datos la lista creada anteriormente.

Para ello se siguieron los siguientes pasos:

1. Acceder al portal de **Power Apps**.
2. Seleccionar la opción **Crear**.
3. Elegir **Aplicación de lienzo**.
4. Seleccionar **Comenzar con datos**.
5. Elegir **Microsoft Lists** como origen de datos.
6. Conectar la lista **FlujoIncidencias**.

Power Apps genera automáticamente una aplicación completamente funcional compuesta por varias pantallas:

* Pantalla principal con el listado de incidencias.
* Pantalla de visualización del detalle.
* Pantalla de creación y edición.

A partir de esa estructura inicial se realizaron pequeñas modificaciones para adaptar la aplicación a las necesidades de la práctica, modificando títulos, controles y botones de navegación.

El formulario quedó conectado directamente con Microsoft Lists, de manera que cada vez que un usuario registra una incidencia mediante el botón **Guardar**, toda la información pasa automáticamente a la lista de SharePoint.

---

# Automatización mediante Power Automate

Una vez terminada la aplicación, el siguiente paso fue automatizar parte del proceso utilizando Power Automate.

En este proyecto se desarrollaron dos flujos diferentes.

---

# Flujo 1. Notificación automática al técnico

## Objetivo

El propósito de este flujo es informar automáticamente al técnico responsable cada vez que un usuario registra una nueva incidencia.

De esta forma se evita que el técnico tenga que revisar continuamente la lista para comprobar si existen nuevas incidencias pendientes.

---

## Creación del flujo

Desde Power Automate se seleccionó la opción:

```
Crear

↓

Flujo automatizado
```

Como desencadenador se utilizó:

```
Cuando se crea un elemento
```

A continuación se configuró la conexión con la lista **FlujoIncidencias**, indicando tanto el sitio de SharePoint como la lista correspondiente.

Una vez configurado el desencadenador, se añadió una nueva acción seleccionando:

```
Enviar correo electrónico (V2)
```

En esta acción se configuró:

**Destinatario**

Correo electrónico del técnico.

**Asunto**

```
Nueva incidencia registrada
```

**Cuerpo del mensaje**

El cuerpo del correo utiliza el contenido dinámico proporcionado por Power Automate para incluir automáticamente información como:

* Usuario que registra la incidencia.
* Fecha de creación.
* Nivel de urgencia.
* Asunto.
* Descripción de la incidencia.

De esta forma, el técnico recibe toda la información necesaria sin necesidad de acceder inicialmente a la aplicación.

---

# Flujo 2. Aviso al usuario cuando la incidencia se resuelve

## Objetivo

El segundo flujo tiene como finalidad informar automáticamente al usuario cuando su incidencia ha sido solucionada.

Este flujo únicamente debe ejecutarse cuando el estado de la incidencia pase a **Resuelta**.

---

## Configuración

Se creó un nuevo flujo automatizado utilizando como desencadenador:

```
Cuando se modifica un elemento
```

Al igual que en el flujo anterior, se seleccionó la lista **FlujoIncidencias** como origen de datos.

Después del desencadenador se añadió una acción **Condición**.

La condición comprueba el valor del campo **EstadoIncidencia**.

```
EstadoIncidencia

es igual que

Resuelta
```

Si la condición se cumple, Power Automate ejecuta la rama **Sí**, donde se añade una acción de envío de correo electrónico.

El destinatario corresponde al usuario que creó la incidencia y el mensaje informa de que la incidencia ha sido resuelta.

Además del aviso, el correo incorpora información adicional obtenida directamente de la lista, como el asunto de la incidencia, la fecha de resolución y el texto introducido por el técnico en el campo correspondiente a la resolución.

En caso de que el estado todavía no sea **Resuelta**, el flujo no realiza ninguna acción y finaliza su ejecución.

---

# Resultado obtenido

Tras integrar Power Apps, Microsoft Lists y Power Automate se obtuvo un sistema completamente funcional para la gestión de incidencias.

El usuario únicamente necesita rellenar un formulario para registrar una incidencia, mientras que el resto del proceso se realiza de forma automática.

Las notificaciones permiten mantener informados tanto al técnico como al usuario durante todo el ciclo de vida de la incidencia, reduciendo el trabajo manual y mejorando el seguimiento de cada solicitud.


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

