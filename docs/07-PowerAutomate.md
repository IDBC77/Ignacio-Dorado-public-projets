# Documentación de los proyectos desarrollados con Microsoft Power Platform
## Introducción

El objetivo de esta práctica ha sido desarrollar tres aplicaciones empresariales utilizando Microsoft Power Platform, integrando Microsoft Lists (SharePoint), Power Apps y Power Automate.

En todos los proyectos se ha empleado Microsoft Lists como repositorio de datos, Power Apps para el desarrollo de la interfaz de usuario y Power Automate para automatizar procesos de negocio mediante flujos de trabajo.

Proyecto 1. Gestor de incidencias informáticas
Descripción

Se ha desarrollado una aplicación destinada a la gestión de incidencias informáticas de una organización.

Los usuarios pueden registrar nuevas incidencias mediante un formulario desarrollado en Power Apps. Cada incidencia queda almacenada automáticamente en una lista de Microsoft Lists, desde donde posteriormente puede ser gestionada.

El sistema contempla el ciclo completo de una incidencia, desde su creación hasta su resolución.

Lista de SharePoint

Se creó una lista denominada FlujoIncidencias con los siguientes campos:

Usuario
Fecha de entrada
Urgencia
Asunto
Cuerpo de la incidencia
Fecha de resolución
Cuerpo de la resolución
Estado de la incidencia
Aplicación Power Apps

La aplicación permite:

Registrar nuevas incidencias.
Consultar el listado de incidencias.
Visualizar la información almacenada.
Gestionar la información directamente sobre la lista de SharePoint.

Toda la información introducida por el usuario es almacenada automáticamente en Microsoft Lists.

Automatización mediante Power Automate
Flujo 1. Aviso al técnico

Cada vez que un usuario registra una nueva incidencia:

Se activa automáticamente el flujo.
Se obtiene la información introducida en el formulario.
Se envía un correo electrónico al técnico informático notificando la existencia de una nueva incidencia.

El correo contiene la información básica necesaria para comenzar su resolución.

Flujo 2. Notificación de resolución

Cuando una incidencia cambia su estado a Resuelta:

El flujo detecta la modificación del elemento.
Comprueba el estado de la incidencia.
Envía automáticamente un correo electrónico al usuario indicando que la incidencia ha sido resuelta.
El correo incluye la resolución introducida por el técnico.
Proyecto 2. Gestión de solicitudes de vacaciones
Descripción

El segundo proyecto consiste en un sistema para gestionar solicitudes de vacaciones entre empleados y responsables.

Los empleados registran una solicitud mediante Power Apps mientras que la aprobación se realiza mediante Microsoft Teams utilizando el sistema de aprobaciones de Power Automate.

Finalmente, el sistema actualiza automáticamente el estado de la solicitud e informa al solicitante mediante correo electrónico.

Lista de SharePoint

Se creó una lista denominada ListaVacas con los siguientes campos:

Empleado
Fecha de inicio
Fecha de fin
Motivo
Estado
Aplicación Power Apps

La aplicación permite:

Registrar nuevas solicitudes de vacaciones.
Consultar el histórico de solicitudes.
Visualizar el estado de cada petición.

Las solicitudes quedan almacenadas automáticamente en Microsoft Lists.

Automatización mediante Power Automate
Flujo 1. Solicitud de aprobación

Cuando un empleado registra una solicitud:

Se inicia automáticamente un flujo de aprobación.
El supervisor recibe una solicitud de aprobación mediante Microsoft Teams.
El supervisor puede aprobar o rechazar la petición.
Flujo 2. Comunicación del resultado

Tras la respuesta del supervisor:

Si la solicitud es aprobada, el empleado recibe un correo electrónico de aceptación.
Si la solicitud es rechazada, el empleado recibe un correo electrónico indicando el rechazo de la petición.
Flujo 3. Actualización automática del estado

En función de la respuesta obtenida durante la aprobación:

Si el resultado es Approve, el campo Estado pasa a Aceptada.
Si el resultado es Reject, el campo Estado pasa a Rechazada.

De esta forma el histórico de solicitudes permanece actualizado automáticamente sin intervención manual.

Proyecto 3. Control de visitas
Descripción

El tercer proyecto consiste en una aplicación destinada al control de visitantes que acceden a la empresa.

La aplicación permite registrar la entrada de visitantes, consultar el histórico de visitas y registrar automáticamente tanto la hora de entrada como la hora de salida.

Lista de SharePoint

Se creó una lista denominada VisitasEmpresa con los siguientes campos:

Nombre
Empresa
Persona a visitar
Hora de entrada
Hora de salida
Estado de la visita
Aplicación Power Apps

La aplicación dispone de dos funcionalidades principales:

Registro de visitantes

Permite registrar una nueva visita mediante un formulario.

Durante el registro:

Se almacenan los datos del visitante.
La hora de entrada se registra automáticamente mediante la función Now().
El estado inicial de la visita se establece como Abierta.
Histórico de visitas

La aplicación permite consultar todas las visitas registradas mostrando:

Visitante.
Empresa.
Persona visitada.
Hora de entrada.
Hora de salida.
Estado de la visita.

Asimismo, incorpora un botón Finalizar visita, cuya finalidad es:

Registrar automáticamente la hora de salida.
Cambiar el estado de la visita a Finalizada.
Automatización mediante Power Automate
Flujo 1. Correo de bienvenida

Cuando un visitante queda registrado:

Se activa automáticamente un flujo.
Se envía un correo electrónico de bienvenida al visitante.
El correo confirma el registro de la visita y proporciona la información básica de la misma.
Flujo 2. Cierre automático de visitas

Se implementa un flujo programado que se ejecuta diariamente al finalizar la jornada laboral.

Durante su ejecución:

Se obtienen todas las visitas cuyo estado continúa siendo Abierta.
Se actualiza automáticamente la hora de salida con la fecha y hora actuales.
El estado de la visita pasa a Finalizada.

Este proceso garantiza que ninguna visita permanezca abierta indefinidamente en el sistema.

Conclusiones

Los tres proyectos desarrollados permiten comprobar la integración existente entre Microsoft Lists, Power Apps y Power Automate para la creación de aplicaciones empresariales sin necesidad de programación tradicional.

La utilización conjunta de estas herramientas permite centralizar la información, automatizar procesos repetitivos y mejorar la comunicación entre usuarios mediante notificaciones automáticas y flujos de aprobación, obteniendo soluciones funcionales orientadas a la digitalización de procesos administrativos.
