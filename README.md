[README - LARAVEL.md](https://github.com/user-attachments/files/33854732/README.-.LARAVEL.md)
# 🧪 Laboratorio Laravel: Implementación del Login y Registro

**Patrón MVC · Autenticación con `laravel/ui` y Bootstrap**

**Universidad Tecnológica de Panamá**
Facultad de Ingeniería de Sistemas Computacionales · Campus Víctor Levis Sasso
Desarrollo Web · II Semestre 2026

## 📖 ¿De qué trata este repositorio?

Este repositorio documenta el laboratorio de **implementación del login y registro de usuarios** en el framework **Laravel** (PHP), del curso de Desarrollo Web. La aplicación permite que un usuario se registre, inicie sesión y cierre sesión, guardando sus datos en una base de datos MySQL.

Aquí se explican los requisitos, los comandos usados paso a paso, los resultados con capturas de pantalla, la base de datos (con su respaldo) y los problemas que surgieron con sus soluciones.

---

## 📑 Tabla de contenido

0. [¿De qué trata?](#-de-qué-trata-este-repositorio)
1. [Requisitos previos](#1--requisitos-previos)
2. [Introducción y arquitectura MVC](#2--introducción-y-arquitectura-mvc)
3. [Instalación y flujo de comandos](#3--instalación-y-flujo-de-comandos)
4. [Resultado visible del laboratorio](#4--resultado-visible-del-laboratorio)
5. [Base de datos](#5--base-de-datos)
6. [Dificultades y soluciones](#6--dificultades-y-soluciones)
7. [Referencias](#7--referencias)
8. [Fecha de ejecución](#8--fecha-de-ejecución)
9. [Estructura del repositorio](#9--estructura-del-repositorio)

---

## 1. 🧰 Requisitos previos

Ecosistema de desarrollo utilizado para el laboratorio:

| Tecnología | Versión / uso |
|---|---|
| **PHP** | 8.3.28 |
| **Composer** | Última versión estable (gestor de dependencias de PHP) |
| **Laravel** | 12.x (creado con el instalador `laravel new`) |
| **MySQL** | 8.4.7 (base de datos) |
| **Apache** | Servidor web, incluido en WampServer |
| **Node.js / npm** | Para compilar los estilos de Bootstrap |
| **Visual Studio Code** | Editor de código |
| **Windows** | Sistema operativo (terminal PowerShell) |

| Requisito | Detalle |
|---|---|
| **PHP** | 8.0 o superior (se usó 8.3.28) |
| **Composer** | Última versión estable |
| **Laravel Installer** | `composer global require laravel/installer` y luego `laravel new` (alternativa: `composer create-project laravel/laravel`) |
| **Entorno de desarrollo local** | WampServer 64 bits (proyectos en `C:\wamp64\www\`) |
| **Servidor web** | Apache |
| **Base de datos** | MySQL 8.4.7 funcionando, administrada con phpMyAdmin 5.2.3 |
| **Editor de código** | Visual Studio Code |
| **Node.js / npm** | Necesario para compilar el *scaffolding* de Bootstrap (`npm install` y `npm run dev`) |
| **Sistema operativo** | Windows |

> 💡 **Recomendación:** en phpMyAdmin, dentro de *Variables y parámetros del servidor*, dejar `storage_engine` en **InnoDB**, que es el motor que usa Laravel por defecto.

---

## 2. 🏗️ Introducción y arquitectura MVC

Este laboratorio es una primera aproximación al framework **Laravel**, que se basa en el patrón **Modelo–Vista–Controlador (MVC)**. Este patrón separa responsabilidades, facilita la reutilización del código y el mantenimiento de las aplicaciones.

**Objetivo del laboratorio:** identificar la estructura básica de un proyecto Laravel, entender cómo se organiza bajo MVC e implementar un módulo de **login y registro** de usuarios.

### Carpetas principales y su rol en MVC

| Capa | Carpeta / archivo | Función |
|---|---|---|
| 🗺️ **Rutas** | `routes/web.php` | Define las URL de la aplicación y a qué controlador llega cada una. |
| 🎮 **Controlador** | `app/Http/Controllers/` | Recibe la petición, aplica la lógica y decide qué vista devolver. Ej.: `Auth\LoginController`, `Auth\RegisterController`. |
| 🧱 **Modelo** | `app/Models/` | Representa una tabla de la base de datos mediante Eloquent. Ej.: `User`. |
| 🖼️ **Vista** | `resources/views/` | Plantillas Blade con el HTML que ve el usuario. Ej.: `auth/login.blade.php`, `auth/register.blade.php`. |
| 🗄️ **Migraciones** | `database/migrations/` | Describen cómo crear o modificar las tablas de la base de datos. |
| ⚙️ **Configuración** | `.env` y `config/` | Variables de entorno (credenciales de BD, puerto, etc.). |

### Comandos de migraciones utilizados

| Comando | Para qué sirve |
|---|---|
| `php artisan migrate` | Ejecuta las migraciones pendientes y las registra en la tabla `migrations`. |
| `php artisan migrate:status` | Muestra qué migraciones ya se ejecutaron. |
| `php artisan migrate:rollback` | Revierte el último lote de migraciones (usa el método `down()`). |
| `php artisan migrate:fresh` | Borra todas las tablas y vuelve a ejecutar todas las migraciones desde cero. |
| `php artisan config:clear` | Limpia la caché de configuración para que Laravel lea el `.env` actualizado. |

---

## 3. ⚙️ Instalación y flujo de comandos

### 3.1 Crear el proyecto

Desde la carpeta del servidor local (`C:\wamp64\www\`):

```bash
laravel new PruebaRegistroLarvel
```

### 3.2 Configurar el archivo `.env`

Antes de migrar se debe crear la base de datos `pruebaregistrolarvel` en phpMyAdmin (cotejamiento `utf8mb4_unicode_ci`) y apuntar el `.env` hacia ella:

```env
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=pruebaregistrolarvel
DB_USERNAME=root
DB_PASSWORD=
```

> ⚠️ Si el puerto de MySQL no es el `3306`, se debe cambiar `DB_PORT`. Nunca subas tu `.env` real al repositorio.

### 3.3 Evitar el error de longitud de cadena

En `app/Providers/AppServiceProvider.php` se agrega el import de `Schema` y se fija la longitud por defecto de los `string` en 191 caracteres:

```php
<?php

namespace App\Providers;

use Illuminate\Support\ServiceProvider;
use Illuminate\Support\Facades\Schema; // ✅ Esta línea

class AppServiceProvider extends ServiceProvider
{
    public function boot(): void
    {
        Schema::defaultStringLength(191);
    }
}
```

### 3.4 Refrescar la configuración y migrar

```bash
php artisan config:clear
php artisan migrate
```

Resultado esperado: se crea la tabla `migrations` y se ejecutan `create_users_table`, `create_cache_table` y `create_jobs_table`.

📎 Captura: `laravel-lab/docs/img/06-migrate-ok.png`

### 3.5 Instalar el paquete de autenticación

**Opción usada: `laravel/ui` con Bootstrap**

```bash
composer require laravel/ui
php artisan ui bootstrap --auth
npm install && npm run dev
php artisan serve
```

La aplicación queda disponible en **http://127.0.0.1:8000**.

**Alternativa recomendada para proyectos nuevos: Laravel Breeze**

```bash
composer require laravel/breeze --dev
php artisan breeze:install
```

> Breeze publica las vistas Blade directamente en `resources/views/auth/` y funciona de forma nativa con Vite. Se documenta como alternativa; no fue la usada en este laboratorio.

### 3.6 Resumen de la secuencia completa

```bash
cd C:\wamp64\www
laravel new PruebaRegistroLarvel
cd PruebaRegistroLarvel
# (crear la BD y editar .env)
php artisan config:clear
php artisan migrate
composer require laravel/ui
php artisan ui bootstrap --auth
npm install && npm run dev
php artisan serve
```

---

## 4. 🖼️ Resultado visible del laboratorio

### Registro de usuario (`/register`)

📎 Captura: `laravel-lab/docs/img/01-registro.jpeg`

### Inicio de sesión (`/login`)

📎 Captura: `laravel-lab/docs/img/02-login.jpeg`

Al registrarse, el usuario queda guardado en la tabla `users` con la contraseña cifrada (hash bcrypt):

📎 Captura: `laravel-lab/docs/img/04-phpmyadmin-users.jpeg`

---

## 5. 🗄️ Base de datos

### Entorno

| Elemento | Valor |
|---|---|
| Motor | MySQL 8.4.7 (servidor `127.0.0.1:3306`) |
| Administrador | phpMyAdmin 5.2.3 |
| Base de datos del Lab #1 | `pruebaregistrolarvel` |
| Motor de tablas | InnoDB |
| Cotejamiento | `utf8mb4_unicode_ci` |

### Cómo interviene Laravel

- **`.env`:** guarda las credenciales y el nombre de la base de datos. Después de editarlo se ejecuta `php artisan config:clear` para que Laravel lea los valores nuevos.
- **Migraciones:** los archivos de `database/migrations/` describen la estructura. Cada migración tiene un método `up()` (crea) y un `down()` (revierte).
- **Tabla `migrations`:** Laravel registra ahí qué migraciones ya se aplicaron para no repetirlas.

### Tablas generadas

Al correr `php artisan migrate` se crearon 9 tablas:

| Tabla | Propósito |
|---|---|
| `users` | Usuarios registrados (nombre, correo, contraseña cifrada) |
| `password_reset_tokens` | Tokens para restablecer contraseña |
| `sessions` | Sesiones de usuario (`SESSION_DRIVER=database`) |
| `cache`, `cache_locks` | Caché en base de datos |
| `jobs`, `job_batches`, `failed_jobs` | Sistema de colas |
| `migrations` | Control de migraciones ejecutadas |

📎 Captura: `laravel-lab/docs/img/03-phpmyadmin-tablas.jpeg`

### Respaldo (backup)

El respaldo generado con phpMyAdmin (*Exportar → SQL*) está en el repositorio:

📁 [`database/backup_pruebaregistrolarvel.sql`](laravel-lab/database/backup_pruebaregistrolarvel.sql)

Para restaurarlo:

```bash
mysql -u root -p -e "CREATE DATABASE pruebaregistrolarvel CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;"
mysql -u root -p pruebaregistrolarvel < database/backup_pruebaregistrolarvel.sql
```

---

## 6. 🚧 Dificultades y soluciones

### Dificultad 1: Error `Table 'jobs' already exists` al migrar

- **Problema:** al ejecutar `php artisan migrate` apareció `SQLSTATE[42S01]: Base table or view already exists: 1050 Table 'jobs' already exists`. El `.env` apuntaba a la base de datos `laravel`, que ya tenía tablas de una prueba anterior.
- **Solución:** se creó una base de datos nueva y limpia (`pruebaregistrolarvel`), se actualizó `DB_DATABASE` en el `.env`, se ejecutó `php artisan config:clear` y se volvió a correr `php artisan migrate`, que terminó sin errores.

📎 Captura: `laravel-lab/docs/img/05-error-migrate.png`

### Dificultad 2: Longitud de cadena en las migraciones

- **Problema:** las columnas `string` pueden provocar errores de longitud de clave con ciertas versiones/configuraciones de MySQL, lo que impide que las migraciones se completen.
- **Solución:** en `AppServiceProvider.php` se importó `Illuminate\Support\Facades\Schema` y se agregó `Schema::defaultStringLength(191);` dentro de `boot()`.

---

## 7. 📚 Referencias

1. Laravel. *Documentación oficial, versión 12.x.* https://laravel.com/docs/12.x
2. Laravel. *laravel/ui: scaffolding de autenticación con Bootstrap.* https://github.com/laravel/ui
3. Composer. *Documentación oficial.* https://getcomposer.org/doc/
4. Fong, I. *Guía de Laboratorio: Cómo crear un Login y Registro en Laravel.* Universidad Tecnológica de Panamá, 2026.
5. Video de apoyo del laboratorio. https://www.youtube.com/watch?v=j5baJsM_Adc

---

## 8. 📅 Fecha de ejecución

**22 de octubre de 2026**

---

## 9. 📂 Estructura del repositorio

```
laravel-lab/
├── README.md
├── database/
│   └── backup_pruebaregistrolarvel.sql
└── docs/
    └── img/
        ├── 01-registro.jpeg
        ├── 02-login.jpeg
        ├── 03-phpmyadmin-tablas.jpeg
        ├── 04-phpmyadmin-users.jpeg
        ├── 05-error-migrate.png
        └── 06-migrate-ok.png
```

---

**Este laboratorio ha sido desarrollado por el estudiante de la Universidad Tecnológica de Panamá:**

- **Nombre:** Erick Elizondro

- **Instructor del Laboratorio:** Irina Fong
