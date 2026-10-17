# Laboratorio Laravel: Login y Registro con Bootstrap (MVC)

**Universidad Tecnológica de Panamá**
Facultad de Ingeniería en Sistemas Computacionales · Campus Víctor Levis Sasso
Asignatura: Desarrollo Web · Docente: Ing. Irina Fong · Tema: Patrón MVC

---

## Índice

1. [Detalles del laboratorio](#1-detalles-del-laboratorio)
2. [Tecnologías y versiones](#2-tecnologías-y-versiones)
3. [Procesos de instalación (paso a paso)](#3-procesos-de-instalación-paso-a-paso)
4. [Controles utilizados](#4-controles-utilizados)
5. [Insertar registros (evidencia)](#5-insertar-registros-evidencia)
6. [Errores encontrados y soluciones](#8-errores-encontrados-y-soluciones)
7. [Estructura MVC del proyecto](#9-estructura-mvc-del-proyecto)
8. [Autor, fecha y referencias](#10-autor-fecha-y-referencias)

---

## 1. Detalles del laboratorio

- **Título de la experiencia:** Primer acercamiento al framework Laravel y exploración de su estructura de trabajo.
- **Tema:** Introducción a Laravel: estructura de carpetas y patrón Modelo–Vista–Controlador (MVC).
- **Objetivo:** Identificar la estructura básica de un proyecto Laravel, comprender su organización bajo el patrón MVC e implementar un sistema de **registro e inicio de sesión** conectado a una base de datos MySQL.
- **Nombre del proyecto:** `Lablaravel3.1`
- **Base de datos:** `lablaravel31`
- **Entorno:** Windows + WampServer (Apache, PHP y MySQL locales).

---

## 2. Tecnologías y versiones

| Tecnología | Versión |
|---|---|
| Sistema operativo | Windows 11 |
| WampServer | 3.4.0 (64 bits) |
| Apache | 2.4.65 |
| PHP | 8.3.28 |
| MySQL | 8.4.7 (puerto 3306) |
| phpMyAdmin | 5.2.3 |
| Composer | 2.10.3 |
| Laravel Installer | 5.32.0 |
| Laravel Framework | 13.36.0 |
| Node.js | 24.21.0 |
| npm | 11.19.0 |
| Vite | 8.3.4 |
| laravel/ui | _(completar con `composer show laravel/ui`)_ |
| Bootstrap | _(completar con `npm list bootstrap`)_ |
| Editor | Visual Studio Code |
| Navegador | Brave |

---

## 3. Procesos de instalación (paso a paso)

### 3.1 Requisitos previos

- WampServer en verde (todos los servicios activos).
- PHP 8.2 o superior (Laravel 13 lo requiere). Se usó **PHP 8.3.28**.
- Composer y Node.js instalados.

### 3.2 Configurar PHP en el PATH de Windows

La terminal estaba usando **PHP 8.0.30**, por lo que Composer instalaba una versión vieja del instalador de Laravel. Cambiar de versión desde el menú de Wamp no actualiza el PATH de Windows.

1. Abrir **Editar las variables de entorno del sistema → Variables de entorno → Path**.
2. Eliminar `C:\wamp64\bin\php\php8.0.30`.
3. Dejar `C:\wamp64\bin\php\php8.3.28`.
4. Agregar `C:\Users\<usuario>\AppData\Roaming\Composer\vendor\bin` (para el comando `laravel`).
5. Cerrar y abrir una PowerShell nueva.

Error inicial (la terminal usaba PHP 8.0.30):

![Error PHP 8.0.30](docs/img/00-error-php-8.0.30.png)

Corrección en la variable Path:

![Variables de entorno](docs/img/00b-path-variables.png)

Verificación:

```powershell
php -v
composer -V
node -v
npm -v
```

> 📷 `docs/img/01-versiones.png`: salida de los cuatro comandos.
>
> ![Versiones](docs/img/01-versiones.png)

### 3.3 Instalar el instalador global de Laravel

```powershell
composer global remove laravel/installer
composer global require laravel/installer
laravel --version
```

> 📷 `docs/img/02-laravel-installer.png`
>
> ![Laravel Installer](docs/img/02-laravel-installer.png)

### 3.4 Crear el proyecto

```powershell
cd C:\wamp64\www
laravel new Lablaravel3.1
```

Respuestas al asistente:

| Pregunta | Respuesta |
|---|---|
| ¿Usar starter kit? | `no` |
| Frontend stack | `Blade` |
| Base de datos | `MySQL` |
| Migraciones por defecto | `No` (primero se crea la base de datos) |

```powershell
cd Lablaravel3.1
php artisan --version
```

> 📷 `docs/img/03-proyecto-creado.png`: mensaje "Application ready" y versión de Laravel.
>
> ![Proyecto creado](docs/img/03-proyecto-creado.png)

### 3.5 Crear la base de datos

En **phpMyAdmin** se creó la base de datos `lablaravel31` con cotejamiento `utf8mb4_unicode_ci`.

> 📷 `docs/img/04-base-de-datos.png`
>
> ![Base de datos](docs/img/04-base-de-datos.png)

### 3.6 Configurar variables de entorno (`.env`)

```env
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=lablaravel31
DB_USERNAME=root
DB_PASSWORD=
```

Después de editar el `.env`:

```powershell
php artisan config:clear
```

### 3.7 Corregir la longitud de cadena

Archivo `app/Providers/AppServiceProvider.php`:

```php
use Illuminate\Support\Facades\Schema;

public function boot(): void
{
    Schema::defaultStringLength(191);
}
```

Además, en `config/database.php`, dentro de la conexión `mysql`, se fijó el motor de almacenamiento en InnoDB (el MyISAM por defecto limita los índices a 1000 bytes):

```php
'engine' => 'InnoDB',
```

### 3.8 Ejecutar migraciones

```powershell
php artisan migrate:fresh
```

Se crean las tablas `users`, `cache`, `jobs` y `migrations`.

> 📷 `docs/img/05-migraciones.png`: migraciones en **DONE**.
>
> ![Migraciones](docs/img/05-migraciones.png)

### 3.9 Instalar autenticación con Bootstrap

```powershell
composer require laravel/ui
php artisan ui bootstrap --auth
npm install
```

### 3.10 Levantar el proyecto

```powershell
composer run dev
```

Este comando inicia en simultáneo el servidor de Laravel (`php artisan serve`), la cola de trabajos y Vite (`npm run dev`).

> 📷 `docs/img/06-composer-run-dev.png`
>
> ![composer run dev](docs/img/06-composer-run-dev.png)

### 3.11 Rutas de prueba

| Ruta | Función |
|---|---|
| `http://localhost:8000/register` | Registro de usuarios |
| `http://localhost:8000/login` | Inicio de sesión |
| `http://localhost:8000/home` | Dashboard (solo usuarios autenticados) |

---

## 4. Controles utilizados

**Controles de formulario (vistas Blade con Bootstrap):**

| Control | Dónde se usa | Descripción |
|---|---|---|
| Campo de texto (`input type="text"`) | Registro: *Name* | Nombre del usuario |
| Campo de correo (`input type="email"`) | Registro y login: *Email Address* | Correo, valor único en la tabla `users` |
| Campo de contraseña (`input type="password"`) | Registro y login: *Password* | Se guarda encriptada (hash bcrypt) |
| Confirmación de contraseña | Registro: *Confirm Password* | Debe coincidir con *Password* |
| Casilla (`checkbox`) | Login: *Remember Me* | Mantiene la sesión iniciada |
| Botones (`button type="submit"`) | *Register*, *Login*, *Logout* | Envían los formularios |
| Enlaces | *Forgot Your Password?*, *Login*, *Register* | Navegación entre vistas |
| Menú desplegable | Barra superior con el nombre del usuario | Contiene *Logout* |

**Otros elementos de Laravel utilizados:**

- Directivas Blade: `@extends`, `@section`, `@csrf`, `@error`, `@auth`, `@guest`.
- Validación de formularios del lado del servidor (mensajes de error por campo).
- Protección CSRF en cada formulario.
- Middleware `auth` para proteger `/home`.
- Comandos Artisan: `migrate`, `migrate:fresh`, `config:clear`, `key:generate`, `ui bootstrap --auth`.

---

## 5. Insertar registros (evidencia)

Al registrar un usuario desde `/register`, Laravel valida los datos y los inserta en la tabla `users`.

**Formulario de registro**

> ![Registro](docs/img/07-registro-formulario.png)

**Validación de campos obligatorios**

![Validación](docs/img/07b-validacion-registro.png)

**Usuario autenticado en el Dashboard (`/home`)**

> ![Dashboard](docs/img/08-home-logueado.png)

**Registro insertado en la base de datos (phpMyAdmin → `lablaravel31` → `users`)**

> ![Tabla users](docs/img/09-tabla-users.png)

**Inicio de sesión (`/login`)**

> ![Login](docs/img/10-login.png)

---

## 6. Errores encontrados y soluciones

| # | Error / problema | Causa | Solución |
|---|---|---|---|
| 1 | `Cannot use laravel/installer's latest version... requires php ^8.2` | La terminal usaba PHP 8.0.30 | Se quitó `php8.0.30` del PATH y se dejó `php8.3.28` |
| 2 | `Table 'users' already exists` (base `laravel`) | El `.env` apuntaba a una base antigua con tablas previas | Se creó `lablaravel31` y se actualizó `DB_DATABASE` |
| 3 | `1071 Specified key was too long; max key length is 1000 bytes` | Índice único del `email` (255 caracteres en `utf8mb4`) excede el límite | `Schema::defaultStringLength(191)` en `AppServiceProvider` |
| 4 | `Table 'users' already exists` tras el error 3 | La migración falló a medias y dejó una tabla parcial | `php artisan migrate:fresh` |
| 5 | La página de bienvenida (`/`) se ve sin estilos | Usa Tailwind y `laravel/ui` reemplaza el CSS por Bootstrap | Es esperado; `/login`, `/register` y `/home` usan Bootstrap y se ven correctamente |
| 6 | Error al escribir `cd` dentro de la pregunta de Laravel Boost | El asistente esperaba una opción del menú, no un comando | Se aceptó la opción por defecto y se esperó a que terminara el instalador |
| 7 | `1071 Specified key was too long` en `failed_jobs` | El motor por defecto era MyISAM (límite de 1000 bytes por índice) | `'engine' => 'InnoDB'` en la conexión `mysql` de `config/database.php` |

**Evidencias de los errores**

![Error 1071](docs/img/17-error-1071.png)

![Tabla users ya existe](docs/img/18-error-users-existe.png)

![Bienvenida sin estilos](docs/img/19-bienvenida-sin-estilos.png)

![Error 1071 en failed_jobs](docs/img/20-error-1071-failed-jobs.png)

---

## 7. Estructura MVC del proyecto

| Capa | Ubicación | Función en este laboratorio |
|---|---|---|
| **Modelo** | `app/Models/User.php` | Representa la tabla `users` |
| **Vista** | `resources/views/auth/` (`login`, `register`) y `resources/views/layouts/app.blade.php` | Formularios y diseño Bootstrap |
| **Controlador** | `app/Http/Controllers/Auth/` y `HomeController.php` | Lógica de registro, login y dashboard |
| **Rutas** | `routes/web.php` | Conecta las URL con los controladores |
| **Migraciones** | `database/migrations/` | Estructura de la base de datos |
| **Configuración** | `.env`, `config/` | Conexión a base de datos y parámetros |

---

## 8. Autor, fecha y referencias

- **Autor:** Jazid Sánchez
- **Docente:** Ing. Irina Fong
- **Fecha:** 17 de octubre de 2026
- **Institución:** Universidad Tecnológica de Panamá, Facultad de Ingeniería en Sistemas Computacionales

**Referencias**

- Fong, I. (2026). *Guía de Laboratorio: Cómo crear un Login y Registro en Laravel*. Universidad Tecnológica de Panamá.
- Laravel. *Documentación oficial*: https://laravel.com/docs
- Laravel UI: https://github.com/laravel/ui
- Composer: https://getcomposer.org
- Node.js: https://nodejs.org
- Vite: https://vite.dev
- Bootstrap: https://getbootstrap.com
- WampServer: https://www.wampserver.com
