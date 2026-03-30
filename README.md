# Prueba Tecnica — Desarrollador Full Stack

**Sistema de Gestion de Tareas**
Backend con Fastify + PostgreSQL | Frontend con React + Redux

---

| Dato | Detalle |
|---|---|
| Duracion estimada | 2 horas |
| Nivel | Senior
| Entregable | Pull Request en el repositorio asignado |
| Formato | Codigo fuente + documentacion minima |

---

## 1. Objetivo

Desarrollar una API REST funcional para la gestion de tareas (To-Do) utilizando Fastify como framework HTTP, PostgreSQL como base de datos relacional y una arquitectura limpia (Clean Architecture) que separe responsabilidades de forma clara. El frontend debera consumir esta API mediante una interfaz construida con React, gestionando el estado global con Redux Toolkit y la autenticacion con JSON Web Tokens (JWT).

---

## 2. Stack Tecnologico

### 2.1 Backend

- Node.js (version 18 o superior)
- Fastify como framework HTTP
- PostgreSQL como motor de base de datos
- Prisma o Knex.js como ORM / query builder
- @fastify/swagger y @fastify/swagger-ui para documentacion de la API
- bcrypt para hashing de contrasenas
- @fastify/jwt para generacion y verificacion de tokens

### 2.2 Frontend

- React 18+
- Redux Toolkit para gestion de estado global
- React Router DOM para navegacion
- Axios o Fetch API para consumo de la API
- Almacenamiento del token JWT en memoria o localStorage

---

## 3. Arquitectura del Backend

El proyecto debe seguir los principios de Clean Architecture. La estructura de carpetas esperada es la siguiente:

```
src/
  domain/
    entities/          # Definicion de entidades (User, Task)
    repositories/      # Interfaces de repositorio (contratos)
  application/
    use-cases/         # Logica de negocio (CreateTask, GetTasks, etc.)
  infrastructure/
    database/          # Conexion a PostgreSQL, migraciones
    repositories/      # Implementaciones concretas de repositorios
  interfaces/
    http/
      routes/          # Definicion de rutas Fastify
      controllers/     # Controladores HTTP
      middlewares/      # Middleware de autenticacion JWT
      schemas/         # Esquemas de validacion (Fastify schemas / Swagger)
  app.js               # Punto de entrada de la aplicacion
```

La capa de dominio no debe depender de frameworks ni librerias externas. La capa de aplicacion contiene los casos de uso que orquestan la logica de negocio. La capa de infraestructura implementa las interfaces definidas en el dominio.

---

## 4. Requerimientos Funcionales

### 4.1 Autenticacion de Usuarios

1. Endpoint POST /api/auth/register para registro de usuarios. Campos requeridos: nombre, correo electronico y contrasena.
2. Endpoint POST /api/auth/login que retorne un JWT valido con expiracion de 1 hora.
3. Hashear las contrasenas con bcrypt antes de almacenarlas.
4. Middleware de autenticacion que valide el token en las rutas protegidas.

### 4.2 Gestion de Tareas (CRUD)

Todos los endpoints de tareas requieren autenticacion. Cada usuario solo puede ver y modificar sus propias tareas.

| Metodo | Ruta | Descripcion |
|---|---|---|
| GET | `/api/tasks` | Listar todas las tareas del usuario autenticado |
| GET | `/api/tasks/:id` | Obtener detalle de una tarea especifica |
| POST | `/api/tasks` | Crear una nueva tarea |
| PUT | `/api/tasks/:id` | Actualizar una tarea existente |
| DELETE | `/api/tasks/:id` | Eliminar una tarea |

### 4.3 Modelo de Datos

**Tabla users:**

| Campo | Tipo | Descripcion |
|---|---|---|
| id | UUID / SERIAL | Clave primaria |
| name | VARCHAR(100) | Nombre del usuario |
| email | VARCHAR(255) | Correo electronico (unico) |
| password | VARCHAR(255) | Contrasena hasheada |
| created_at | TIMESTAMP | Fecha de creacion |

**Tabla tasks:**

| Campo | Tipo | Descripcion |
|---|---|---|
| id | UUID / SERIAL | Clave primaria |
| title | VARCHAR(200) | Titulo de la tarea |
| description | TEXT | Descripcion detallada (opcional) |
| completed | BOOLEAN | Estado de la tarea (default: false) |
| user_id | FK -> users.id | Relacion con el usuario propietario |
| created_at | TIMESTAMP | Fecha de creacion |
| updated_at | TIMESTAMP | Fecha de ultima actualizacion |

### 4.4 Documentacion con Swagger

1. Configurar @fastify/swagger y @fastify/swagger-ui.
2. Documentar todos los endpoints con sus esquemas de request y response.
3. La documentacion debe ser accesible en la ruta /documentation.
4. Incluir ejemplos de payloads en cada esquema.

---

## 5. Requerimientos del Frontend

### 5.1 Pantallas Requeridas

1. **Login:** Formulario de inicio de sesion (correo y contrasena). Al autenticarse correctamente, almacenar el JWT y redirigir al listado de tareas.
2. **Registro:** Formulario de registro con validacion basica en el cliente (campos obligatorios, formato de correo).
3. **Listado de Tareas:** Vista principal que muestre las tareas del usuario con opciones para marcar como completada, editar y eliminar.
4. **Formulario de Tarea:** Modal o pantalla separada para crear y editar tareas (titulo, descripcion).

### 5.2 Gestion de Estado con Redux Toolkit

1. Crear un slice para autenticacion (authSlice) que gestione el token JWT, datos del usuario y estado de carga.
2. Crear un slice para tareas (tasksSlice) con acciones asincronas (createAsyncThunk) para las operaciones CRUD.
3. Utilizar el store de Redux como fuente unica de verdad para el estado de la aplicacion.

### 5.3 Autenticacion JWT en el Frontend

1. Enviar el token en el header `Authorization: Bearer <token>` en cada solicitud protegida.
2. Implementar un interceptor o wrapper de Axios/Fetch que adjunte el token automaticamente.
3. Redirigir al login si el token no existe o ha expirado.
4. Implementar una ruta protegida (PrivateRoute) que impida el acceso a usuarios no autenticados.

---

## 6. Criterios de Evaluacion

| Criterio | Peso | Detalle |
|---|---|---|
| Separacion de capas (Clean Architecture) | 25% | Dominio, aplicacion e infraestructura claramente delimitados |
| Funcionalidad completa de la API | 20% | CRUD de tareas, autenticacion, validaciones |
| Integracion del frontend con Redux y JWT | 20% | Slices correctos, flujo de autenticacion funcional |
| Documentacion Swagger | 10% | Endpoints documentados con esquemas y ejemplos |
| Calidad del codigo | 15% | Nombres claros, consistencia, manejo de errores |
| Configuracion y entrega del proyecto | 10% | README, variables de entorno, Pull Request correcto |

---

## 7. Instrucciones de Entrega

El proceso de entrega se realiza mediante un Pull Request en GitHub. A continuacion se detallan los pasos obligatorios:

### 7.1 Preparacion del Repositorio

Se proporcionara al candidato acceso a un repositorio base en GitHub. Este repositorio contiene unicamente un archivo README inicial en la rama `main`. No se debe trabajar directamente sobre `main`.

### 7.2 Creacion de la Rama de Trabajo

Clonar el repositorio y crear una rama con el siguiente formato de nombre:

```
git clone <url-del-repositorio>
cd <nombre-del-repositorio>
git checkout -b prueba/<nombre-apellido>
```

Ejemplo: `prueba/juan-perez`

Todo el desarrollo debe realizarse sobre esta rama.

### 7.3 Estructura del Repositorio

El repositorio entregado debe contener la siguiente estructura minima:

```
/
  backend/
    src/
      domain/
      application/
      infrastructure/
      interfaces/
    package.json
    .env.example
  frontend/
    src/
      store/         # Configuracion de Redux
      slices/        # authSlice, tasksSlice
      components/
      pages/
    package.json
  README.md
```

### 7.4 Archivo README.md

El README del candidato debe incluir como minimo los siguientes apartados: instrucciones para instalar dependencias del backend y frontend, configuracion de variables de entorno (referencia al archivo .env.example), comandos para ejecutar migraciones o crear las tablas en PostgreSQL, comandos para levantar el backend y el frontend en modo desarrollo, y cualquier decision tecnica relevante que haya tomado durante el desarrollo.

### 7.5 Envio del Pull Request

Una vez completado el desarrollo, el candidato debe subir su rama al repositorio remoto y crear un Pull Request hacia la rama `main`:

```
git add .
git commit -m "Prueba tecnica - <Nombre Apellido>"
git push origin prueba/<nombre-apellido>
```

El Pull Request debe cumplir con lo siguiente:

1. **Titulo:** Prueba Tecnica - Nombre Apellido
2. **Descripcion:** Incluir un resumen breve de lo implementado, las tecnologias utilizadas, y cualquier consideracion especial.
3. **Rama origen:** `prueba/<nombre-apellido>`
4. **Rama destino:** `main`
5. **Estado:** No fusionar (merge). El equipo evaluador revisara el PR tal como fue enviado.

### 7.6 Plazo de Entrega

El Pull Request debe estar creado dentro del plazo comunicado por el equipo de seleccion. Se tomara como referencia la fecha y hora del ultimo commit en la rama y la hora de creacion del Pull Request.

---

## 8. Consideraciones Adicionales

- No se requiere despliegue en produccion; basta con que el proyecto funcione en entorno local.
- Se permite el uso de librerias auxiliares (validacion, CORS, dotenv) siempre que se justifique.
- El diseno visual del frontend no sera evaluado; se valora la funcionalidad y la estructura del codigo.
- Se recomienda hacer commits frecuentes con mensajes descriptivos para evidenciar el proceso de desarrollo.
- Ante cualquier duda sobre los requerimientos, documentar la decision tomada en el README.

---

## 9. Flujo Esperado de la Aplicacion

1. El usuario accede a la pantalla de registro y crea una cuenta.
2. El sistema almacena el usuario con la contrasena hasheada en PostgreSQL.
3. El usuario inicia sesion y el backend retorna un JWT.
4. El frontend almacena el token en el estado de Redux (authSlice).
5. El usuario crea, edita, completa y elimina tareas desde la interfaz.
6. Cada solicitud al backend incluye el JWT en el header de autorizacion.
7. El backend valida el token, identifica al usuario y ejecuta la operacion solicitada.

---

## 10. Puntos Extra (Opcionales)

Los siguientes elementos no son obligatorios, pero seran valorados positivamente si el candidato los implementa dentro del tiempo establecido:

- Pruebas unitarias sobre al menos un caso de uso del backend.
- Paginacion en el endpoint de listado de tareas (query params: page, limit).
- Filtro por estado de la tarea (completada / pendiente).
- Manejo centralizado de errores con codigos HTTP adecuados y mensajes consistentes.
- Uso de Docker y docker-compose para levantar la base de datos y la aplicacion.

---

*Departamento de Tecnologia*
*Este documento es confidencial y de uso exclusivo para el proceso de seleccion.*
