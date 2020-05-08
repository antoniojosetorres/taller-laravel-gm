## Apuntes del _Taller de Laravel_, de Gustavo A Meza G

## 01 MVC
* [DB] <---> [MODEL] <---> [CONTROLLER] ---> [VIEW] ---> [User]
* [User] ---> [Routes] -(Middleware)-> [CONTROLLER] ---> [VIEW] ---> [User]
* [User] ---> [Routes] -(Middleware)-> [CONTROLLER] <-(Repositories)-> [MODEL] <---> [DB]

## 05 Instalar Entorno de Desarrollo con XAMPP en Windows
* Descargar XAMPP de <https://sourceforge.net/projects/xampp/files/XAMPP%20Windows/7.4.4/xampp-windows-x64-7.4.4-1-VC15-installer.exe/download>

## 06 Instalar Composer en Windows
* Descargar Composer de <https://getcomposer.org/Composer-Setup.exe>

## 07 Instalar Laravel en Windows
* Ejecutar XAMPP en `u:\xampp` >>> `xampp-control`.  

* Instalar Laravel mediante Composer desde el terminal:
    * Ejecutar Composer en `u:\xampp\htdocs` >>> `composer create-project laravel/laravel [proyecto]`

* Iniciar Laravel desde el terminal:  
    * Cambiar a la carpeta `[proyecto]` >>> `cd [proyecto]`  
    * _(Opcional)_ Ejecutar el servidor de Laravel >>> `php artisan serve`

* En Chrome:  
    * Usando el servidor Apache >>> `localhost/[proyecto]/public`   
    * _(Opcional)_ Usando el servidor de Laravel >>> `localhost:8000`

## 08 Configurar Virtual Host con XAMPP en Windows
* Editar `u:\xampp\apache\conf\extra\httpd-vhosts.conf` y añadir las lineas:
```xml
    <VirtualHost *:80> 
        DocumentRoot "u:/xampp/htdocs/"
        ServerName localhost
    </VirtualHost>
    <VirtualHost *:80>
        DocumentRoot "u:/xampp/htdocs/[proyecto]/public"
        ServerName [dominio]
    </VirtualHost>
```
* Reiniciar Apache desde XAMPP.  
* Editar `c:/windows/system32/drivers/etc/hosts` y añadir la linea `127.0.0.1  [dominio]`.  
* En Chrome, abrir `http://[dominio]`.

## 12 Estructura de Directorios (Laravel 7)
```
    [app]
        [Console]   //consola
            Kernel.php
        [Exceptions]   //excepciones
            Handler.php
        [Http]
            [Controllers]   //controladores
                [Auth]
                Controller.php
            [Middleware]
            Kernel.php
        [Providers]
            RouteServiceProvider.php
        User.php
    [bootstrap]
        [cache]
		app.php
    [config]   //configuracion
    [database]   //base de datos
        [factories]
        [migrations]   //migraciones
        [seeds]
    [public]
        [css]
        [js]
        index.php
    [resources]   //recursos
        [js]
        [lang]   //idioma
            [en]   //ingles
                auth.php
                pagination.php
                passwords.php
                validation.php
        [sass]
        [views]   //vistas
            welcome.blade.php  
    [routes]   //rutas
        api.php
        channels.php
        console.php
        webs.php
    [storage]   //almacenamiento
        [app]
            [public]
        [framework]
            [cache]
            [sessions]
            [views]
        [logs]
    [tests]   //pruebas
    [vendor]   //proveedor (complemento)
```

* _(Recomendado)_ Crear carpeta `[Models]` dentro de la carpeta `[app]` para incluir nuestros modelos.  
* En versiones de Laravel anteriores a la 7:  
```
    [resources]
        [assets]
            [js]
            [sass]
```
* En `routes/web.php` incluiremos nuestras rutas.

## 14 Configurar Base de Datos en Laravel
* Editar fichero `.env`.
* Los parámetros a cambiar son:  
```
    DB_HOST=127.0.0.1
    DB_DATABASE=laravel
    DB_USERNAME=root
    DB_PASSWORD=
```
* Para el entorno de desarrollo:  
	* `DB_HOST`: dirección IP de `localhost`.
	* `DB_DATABASE`: nombre de la base de datos, por ejemplo `laravel`.
	* `DB_USERNAME`: usuario `root`.
	* `DB_PASSWORD`: se deja en blanco porque no hay contraseña en phpMyAdmin.

* Crear la base de datos en phpMyAdmin:
	* Ir a `Bases de datos > Crear base de datos`.
	* Escribir `laravel` (por ejemplo).
	* Pulsar boton `Crear`.

## 15 Generar Llave de la Aplicacion para el Archivo .env
* Copiar fichero `.env.example` como `.env`.
* Ejecutar desde el terminal `php artisan key:generate`.

## 16 Que son las Migraciones
Las **migraciones** son como un control de versiones para la base de datos que permite modificar y compartir el esquema de la base de datos de la aplicacion.

* Ir a `[database]/[migrations]`.
* Comprobar fichero `2014_10_12_000000_create_users_table.php`.
* Este fichero permite crear una tabla `users` en la base de datos.
```php
    public function up()
    {
        Schema::create 'users', function (Blueprint $table)
        {
            $table->bigIncrements('id');
            $table->string('name');
            $table->string('email')->unique();
            $table->timestamp('email_verified_at')->nullable();
            $table->string('password');
            $table->rememberToken();
            $table->timestamps();
		});
	}
```
* El metodo `up()` se usa para añadir tablas nuevas, columnas o indices a la base de datos.
* El metodo `down()` se usa para eliminar tablas, columnas o indices de la base de datos.
* Los campos de la tabla `users` son:
	* `id`
	* `name`
	* `email`
	* `email_verified_at`
	* `password`

* La línea `$table->timestamps();` equivale a:  
	* `$table->timestamp('created_at');`
	* `$table->timestamp('updated_at');`

## 17 Migracion de la Tabla users
* Añadir dos nuevos campos debajo de `password` (necesarios en el ejemplo):
```php
            $table->integer('type')->default(2);
            $table->boolean('active')->default(true);
```
* Ejecutar `php artisan migrate`.
* Comprobar en phpMyAdmin que se han creado las tablas y los campos correspondientes.
* En caso de error puede deshacerse la migracion desde el terminal mediante `php artisan migrate:rollback`.

## 18 Creando Nueva Migracion para las Tareas (tasks)
* El ejemplo está basado en el tutorial _Intermediate Task List_ de la version 5.2 de Laravel.
* Ejecuta desde el terminal `php artisan make:migration create_tasks_table`.  
* Añadir 3 nuevos campos debajo de `id` (necesarios en el ejemplo):
```php
            $table->integer('user_id')->unsigned();
            $table->string('name');
            $table->boolean('completed')->default(false);
```
* Añadir como último campo:
```php
            $table->foreign('user_id')->references('id')->on('users')->onDelete('cascade');
```
* Ejecutar desde el terminal `php artisan migrate`.

## 19 Que son los Modelos. El Modelo user
Los **modelos** permiten consultar datos en sus tablas así como insertar nuevos registros en la tabla.  

* Recomendable, crear una carpeta `[Models]` dentro de la carpeta `[app]`.
* Mover `User.php` dentro de la carpeta `[app]`.
* Cambiar en `User.php` la línea `namespace App` por `namespace App\Models`.

## 20 Que es PSR-2, PSR-4 y Namespace
* **PSR-2** es la Guia de estilo de codigo. Está orientado a dar formato al código PHP.
	<https://github.com/jatubio/5minutos_laravel/wiki/Estandares-de-programacion.-PSR-2>
* PSR-2 sigue el estandar **PSR-1** (Estandar basico de estilos de codigo).
	<https://github.com/jatubio/5minutos_laravel/wiki/Estandares-de-programacion.-PSR-1-y-PSR-4>  
* **PSR-4** es el estandar de Autocarga. Esta orientado a facilitar la carga automática de clases.  

* Las rutas completas de los Namespaces deben ser: `VendorName\Namespace\ClassName`. Ejemplo:
```php 
    use Illuminate\Database\Schema\Blueprint;
    // VendorName: Illuminate
    // Namespace: Database\Schema
    // ClassName: Blueprint

    use Illuminate\Database\Migrations\Migration;
    // VendorName: Illuminate
    // Namespace: Database\Migrations
    // ClassName: Migration
```  
* En Laravel es opcional seguir los estándares PSR-1 y PSR-2 pero es recomendable seguir PSR-4.

## 21 Cambiar el Namespace de Aplicacion
* Comprobar el fichero `composer.json`.
* Ir al apartado `autoload`:
```json
        "autoload": {
            "psr-4": {
                "App\\": "app/"
            },
            "classmap": [
                "database/seeds",
                "database/factories"
            ]
        },
```
* Dentro de la opción `psr-4` se indica el nombre de la aplicacion `App` y la carpeta donde se encuentra `app`.

* Ver el fichero `Controller.php` dentro de la carpeta `app\Http\Controllers` como ejemplo:
```php
    <?php

    namespace App\Http\Controllers;

    use Illuminate\Foundation\Auth\Access\AuthorizesRequests;
    use Illuminate\Foundation\Bus\DispatchesJobs;
    use Illuminate\Foundation\Validation\ValidatesRequests;
    use Illuminate\Routing\Controller as BaseController;

    class Controller extends BaseController
    {
        use AuthorizesRequests, DispatchesJobs, ValidatesRequests;
    }
```
* Namespace: `App\Http\Controllers` donde va la clase `Controller` (`App` se escribe en mayuscula aunque la carpeta se escriba `app` en minuscula).
* Clases externas (se llaman mediante `use`):
	* `AuthorizesRequests` dentro de `Illuminate\Foundation\Auth\Access`.
	* `DispatchesJobs` dentro de `Illuminate\Foundation\Bus`.
	* `ValidatesRequests` dentro de `Illuminate\Foundation\Validation`.
	* `Controller` dentro de `Illuminate\Routing` renombrado como `BaseController` para no confundirlo con la propia clase `Controller` (se crea un alias usando `as`).

* Para cambiar el nombre de la aplicacion `App` a `Proyecto`, se ejecuta desde el terminal `php artisan app:name Proyecto`. De esta manera, se cambia el namespace de todos los ficheros afectados dela aplicacion.

## 22 Creando el Modelo task
* Ejecutar desde el terminal `php artisan make:model Models/Task`.

## 23
