# Laravel Multi-Auth Package - Breeze Integration Guide

## Overview
This package provides multi-authentication scaffolding for Laravel 12 with full Laravel Breeze integration. It supports Blade, React (Inertia), and Vue (Inertia) stacks.

## Features
- ✅ Automatic Breeze detection and installation
- ✅ Multi-stack support (Blade, React, Vue)
- ✅ Automatic stack detection
- ✅ Complete scaffolding (Models, Migrations, Controllers, Routes, Views)
- ✅ Guard-specific authentication
- ✅ Easy to use and user-friendly

## Installation

### 1. Install the Package
```bash
composer require chweb/multi-auth --dev
```

### 2. Run the Install Command
```bash
php artisan multi-auth:install {guard-name}
```

Example:
```bash
php artisan multi-auth:install admin
```

## How It Works

### Breeze Detection
The package automatically checks if Laravel Breeze is installed by examining your `composer.json` file.

**If Breeze is NOT installed:**
- You'll be prompted to install it
- You can choose your preferred stack (blade, react, vue, api)
- The package will install Breeze and the selected stack automatically

**If Breeze IS installed:**
- The package detects your current stack from `package.json`
- It generates guard-specific files matching your stack

### Stack Detection
The package automatically detects your stack:
- **React**: Checks for `@inertiajs/react` in dependencies
- **Vue**: Checks for `@inertiajs/vue3` in dependencies
- **Blade**: Default if no Inertia dependencies found

## Generated Files

### For All Stacks
- **Model**: `app/Models/{Guard}.php`
- **Migration**: `database/migrations/{timestamp}_create_{guards}_table.php`
- **Routes**: `routes/{guard}.php`

### Blade Stack
- **Controllers**: `app/Http/Controllers/{Guard}/LoginController.php`, `DashboardController.php`
- **Views**: `resources/views/{guard}/login.blade.php`, `dashboard.blade.php`, `layouts/app.blade.php`, `layouts/navigation.blade.php`

### React Stack (Inertia)
- **Controllers**: `app/Http/Controllers/{Guard}/LoginController.php` (Inertia-based), `DashboardController.php`
- **Views**: `resources/js/Pages/{Guard}/Login.jsx`, `Dashboard.jsx`

### Vue Stack (Inertia)
- **Controllers**: `app/Http/Controllers/{Guard}/LoginController.php` (Inertia-based), `DashboardController.php`
- **Views**: `resources/js/Pages/{Guard}/Login.vue`, `Dashboard.vue`

## Configuration

After running the install command, add the following to your `config/auth.php`:

```php
'guards' => [
    'admin' => [
        'driver' => 'session',
        'provider' => 'admins',
    ],
],

'providers' => [
    'admins' => [
        'driver' => 'eloquent',
        'model' => App\Models\Admin::class,
    ],
],

'passwords' => [
    'admins' => [
        'provider' => 'admins',
        'table' => 'password_reset_tokens',
        'expire' => 60,
        'throttle' => 60,
    ],
],
```

## Route Registration

Register your guard routes in `bootstrap/app.php` or `RouteServiceProvider`:

```php
// bootstrap/app.php
->withRouting(
    web: __DIR__.'/../routes/web.php',
    commands: __DIR__.'/../routes/console.php',
    health: '/up',
    then: function () {
        Route::middleware('web')
            ->group(base_path('routes/admin.php'));
    },
)
```

## Usage Examples

### Creating an Admin Guard
```bash
php artisan multi-auth:install admin
```

### Creating a Manager Guard
```bash
php artisan multi-auth:install manager
```

### Overwriting Existing Files
```bash
php artisan multi-auth:install admin --force
```

## Routes Generated

For a guard named `admin`, the following routes are created:

- `GET /admin/login` - Show login form
- `POST /admin/login` - Process login
- `POST /admin/logout` - Logout
- `GET /admin/dashboard` - Dashboard (protected)

## Middleware

The generated controllers use the appropriate guard:
```php
Auth::guard('admin')->attempt($credentials)
```

## Stack-Specific Notes

### Blade
- Uses traditional Blade templates
- Includes layouts and navigation partials
- Styled with Tailwind CSS (from Breeze)

### React (Inertia)
- Uses React components with Inertia.js
- Leverages Breeze's existing components (Checkbox, TextInput, etc.)
- Follows Breeze's component structure

### Vue (Inertia)
- Uses Vue 3 components with Inertia.js
- Leverages Breeze's existing components
- Follows Breeze's component structure

## Troubleshooting

### Breeze Installation Fails
If automatic Breeze installation fails, install it manually:
```bash
composer require laravel/breeze --dev
php artisan breeze:install blade
```
Then run the multi-auth install command again.

### Wrong Stack Detected
If the wrong stack is detected, ensure your `package.json` has the correct dependencies:
- React: `@inertiajs/react`
- Vue: `@inertiajs/vue3`

## Version
Current version: **1.0.0**

Access programmatically:
```php
use Chweb\MultiAuth\MultiAuthServiceProvider;

$version = MultiAuthServiceProvider::VERSION;
```

## License
MIT License

## Support
For issues and feature requests, please visit the GitHub repository.
