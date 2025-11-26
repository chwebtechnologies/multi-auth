# Multi-Auth Implementation Guide

This guide explains how to install and use the `chweb/multi-auth` package in your Laravel 12 application.

## Prerequisites
- PHP 8.4
- Laravel 11.x or 12.x

## Installation

1.  **Require the package** (Local development):
    Since this is a local package for now, add it to your `composer.json` repositories:
    ```json
    "repositories": [
        {
            "type": "path",
            "url": "./path/to/chweb-multi-auth"
        }
    ],
    ```
    Then run:
    ```bash
    composer require chweb/multi-auth
    ```

2.  **Register the Service Provider** (Automatic in Laravel 11+):
    Laravel should auto-discover the package. If not, add `Chweb\MultiAuth\MultiAuthServiceProvider::class` to `bootstrap/providers.php` or `config/app.php`.

## Usage

To create a new authentication guard (e.g., for `Admin`), run the following command:

```bash
php artisan multi-auth:install admin
```

This command will generate:
- `App/Models/Admin.php`
- `database/migrations/xxxx_xx_xx_create_admins_table.php`
- `App/Http/Controllers/Admin/LoginController.php`
- `App/Http/Controllers/Admin/DashboardController.php`
- `routes/admin.php`
- `resources/views/admin/login.blade.php`
- `resources/views/admin/dashboard.blade.php`
- `resources/views/admin/layouts/app.blade.php`

## Post-Installation Steps

After running the command, you need to perform a few manual steps to wire everything up.

### 1. Update `config/auth.php`

Add the new guard and provider to your `config/auth.php` file.

```php
'guards' => [
    // ...
    'admin' => [
        'driver' => 'session',
        'provider' => 'admins',
    ],
],

'providers' => [
    // ...
    'admins' => [
        'driver' => 'eloquent',
        'model' => App\Models\Admin::class,
    ],
],

'passwords' => [
    // ...
    'admins' => [
        'provider' => 'admins',
        'table' => 'password_reset_tokens',
        'expire' => 60,
        'throttle' => 60,
    ],
],
```

### 2. Register Routes

Register the generated route file.
In `bootstrap/app.php` (Laravel 11+):

```php
return Application::configure(basePath: dirname(__DIR__))
    ->withRouting(
        web: __DIR__.'/../routes/web.php',
        commands: __DIR__.'/../routes/console.php',
        health: '/up',
        then: function () {
            Route::middleware('web')
                ->group(base_path('routes/admin.php'));
        },
    )
    // ...
```

### 3. Run Migrations

Run the migrations to create the new table.

```bash
php artisan migrate
```

### 4. Test

Visit `/admin/login` in your browser. You should see the login page.
You can create a test admin user using Tinker:

```bash
php artisan tinker
```

```php
App\Models\Admin::create([
    'name' => 'Super Admin',
    'email' => 'admin@example.com',
    'password' => bcrypt('password'),
]);
```

Now try logging in!
