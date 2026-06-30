# Microsoft Power Platform - Proyectos de Automatización

## Introducción

En esta práctica se han desarrollado tres aplicaciones utilizando **Microsoft Power Platform**, integrando las siguientes herramientas:

* **Microsoft Lists (SharePoint)** como base de datos.
* **Power Apps** para el desarrollo de las aplicaciones.
* **Power Automate** para automatizar los procesos de negocio.

Cada proyecto persigue digitalizar un proceso habitual dentro de una empresa mediante formularios, almacenamiento de información y automatizaciones.

---

# Proyecto 1 - Gestor de incidencias informáticas

## Objetivo

Desarrollar una aplicación que permita registrar incidencias informáticas y automatizar las notificaciones entre usuarios y técnicos.

---

## Arquitectura del proyecto

```text
Usuario
   │
   ▼
Power Apps
   │
   ▼
Microsoft Lists
   │
   ├──────────────► Power Automate
   │                    │
   │                    ▼
   │              Correo al técnico
   │
   └────────────────────► Actualización de la incidencia
                                │
                                ▼
                         Correo al usuario
```

---

# Creación de la base de datos

Se creó una lista de SharePoint denominada **FlujoIncidencias**.

Las columnas creadas fueron:

| Campo            | Tipo             |
| ---------------- | ---------------- |
| Usuario          | Persona          |
| FechaEntrada     | Fecha y hora     |
| Urgencia         | Elección         |
| Asunto           | Texto            |
| CuerpoIncidencia | Texto multilínea |
| FechaResolucion  | Fecha y hora     |
| CuerpoResolucion | Texto multilínea |
| EstadoIncidencia | Elección         |

---

# Desarrollo de la aplicación

Desde **Power Apps** se seleccionó:

```
Crear
      ↓
Aplicación de lienzo
      ↓
Conectar origen de datos
      ↓
Microsoft Lists
      ↓
FlujoIncidencias
```

Power Apps generó automáticamente:

* Pantalla principal.
* Pantalla de consulta.
* Pantalla de edición.
* Formulario de alta.

Posteriormente se personalizó la aplicación modificando controles, títulos y botones de navegación.

---

# Flujo 1 - Aviso al técnico

## Objetivo

Cada vez que un usuario registre una incidencia, el técnico debe recibir un correo electrónico automáticamente.

## Configuración

### 1. Crear un flujo automatizado

```
Power Automate

Crear

Flujo automatizado
```

---

### 2. Disparador

Seleccionar:

```
Cuando se crea un elemento
```

Lista:

```
FlujoIncidencias
```

---

### 3. Acción

Agregar acción

```
Enviar correo electrónico (V2)
```

Configurar:

* Destinatario: Técnico.
* Asunto: Nueva incidencia registrada.
* Cuerpo: Información obtenida de la lista mediante contenido dinámico.

---

# Flujo 2 - Resolución de incidencias

## Objetivo

Cuando la incidencia cambie su estado a **Resuelta**, el usuario recibirá un correo indicando que la incidencia ha finalizado.

## Configuración

### Disparador

```
Cuando se modifica un elemento
```

Lista:

```
FlujoIncidencias
```

---

### Condición

```
EstadoIncidencia

es igual que

Resuelta
```

---

### Rama "Sí"

Agregar acción:

```
Enviar correo electrónico (V2)
```

Destinatario:

```
Usuario (Email)
```

El cuerpo del correo incluye:

* Asunto.
* Fecha de resolución.
* Resolución de la incidencia.

---

# Proyecto 2 - Gestión de vacaciones

## Objetivo

Automatizar el proceso de solicitud y aprobación de vacaciones mediante Microsoft Teams.

---

## Arquitectura

```text
Empleado

Power Apps

Microsoft Lists

Power Automate

Aprobación Teams

Supervisor

Actualizar estado

Correo al empleado
```

---

# Base de datos

Lista:

```
ListaVacas
```

Campos:

| Campo       | Tipo     |
| ----------- | -------- |
| Empleado    | Persona  |
| FechaInicio | Fecha    |
| FechaFin    | Fecha    |
| Motivo      | Texto    |
| Estado      | Elección |

Opciones del estado:

* Pendiente
* Aceptada
* Rechazada

---

# Desarrollo de la aplicación

Se creó un formulario para registrar solicitudes y una pantalla para consultar el histórico.

La información se almacena automáticamente en SharePoint.

---

# Flujo de aprobación

## Paso 1

Crear flujo automatizado.

Disparador:

```
Cuando se crea un elemento
```

---

## Paso 2

Agregar acción:

```
Iniciar y esperar una aprobación
```

Tipo:

```
Aprobar / Rechazar
```

Responsable:

Supervisor.

---

## Paso 3

Agregar una condición.

```
Outcome

es igual que

Approve
```

---

## Rama "Sí"

Acciones:

1. Enviar correo al empleado.
2. Actualizar elemento.

En la actualización se modifica:

```
Estado

↓

Aceptada
```

---

## Rama "No"

Acciones:

1. Enviar correo indicando rechazo.
2. Actualizar elemento.

```
Estado

↓

Rechazada
```

De esta forma el estado de la solicitud queda sincronizado automáticamente con la decisión del supervisor.

---

# Proyecto 3 - Control de visitas

## Objetivo

Registrar las visitas realizadas a la empresa y controlar automáticamente su entrada y salida.

---

## Arquitectura

```text
Visitante

Power Apps

Microsoft Lists

Power Automate

Correo bienvenida

Finalizar visita

Cierre automático
```

---

# Base de datos

Lista:

```
VisitasEmpresa
```

Campos:

| Campo          | Tipo         |
| -------------- | ------------ |
| Nombre         | Texto        |
| Empresa        | Texto        |
| PersonaVisitar | Texto        |
| HoraEntrada    | Fecha y hora |
| HoraSalida     | Fecha y hora |
| EstadoVisita   | Elección     |

Estados:

* Abierta
* Finalizada

---

# Desarrollo de la aplicación

La aplicación dispone de dos funcionalidades principales.

## Registro de visitantes

El usuario introduce:

* Nombre.
* Empresa.
* Persona a visitar.

Los campos **HoraEntrada** y **EstadoVisita** se generan automáticamente mediante Power Apps utilizando la función:

```PowerApps
Now()
```

El estado inicial queda establecido como:

```
Abierta
```

---

## Histórico

La aplicación incorpora una pantalla de consulta donde pueden visualizarse todas las visitas registradas.

---

## Finalización manual

Se añadió un botón denominado:

```
Finalizar visita
```

Al pulsarlo se ejecuta una actualización sobre el registro seleccionado que:

* Guarda la hora actual como hora de salida.
* Cambia el estado de la visita a **Finalizada**.

---

# Flujo de bienvenida

## Disparador

```
Cuando se crea un elemento
```

Lista:

```
VisitasEmpresa
```

---

## Acción

```
Enviar correo electrónico (V2)
```

El visitante recibe un correo de confirmación con:

* Nombre.
* Empresa.
* Persona a visitar.
* Hora de entrada.

---

# Flujo de cierre automático

Para evitar que permanezcan visitas abiertas indefinidamente, se implementó un flujo programado.

## Configuración

Crear

```
Flujo programado
```

Frecuencia:

```
Diaria
```

Hora:

```
20:00
```

---

## Obtener elementos

Se obtienen todas las visitas cuyo estado sea:

```
Abierta
```

---

## Actualizar elemento

Para cada visita abierta:

* HoraSalida = Fecha y hora actual.
* EstadoVisita = Finalizada.

Con ello se garantiza la consistencia de la información incluso cuando no se pulse manualmente el botón **Finalizar visita**.

---

# Conclusiones

Los tres proyectos desarrollados demuestran la integración existente entre **Microsoft Lists**, **Power Apps** y **Power Automate** para construir aplicaciones empresariales sin necesidad de programación tradicional.

La utilización conjunta de estas herramientas permite centralizar la información, automatizar procesos repetitivos, gestionar aprobaciones, enviar notificaciones automáticas y mantener actualizados los datos almacenados en SharePoint, proporcionando soluciones eficientes para la digitalización de procesos empresariales.
