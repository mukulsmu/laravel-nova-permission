# A Laravel Nova tool for the Spatie Permission library

[![Packagist License](https://poser.pugx.org/insenseanalytics/laravel-nova-permission/license.png)](http://choosealicense.com/licenses/mit/)
[![Latest Stable Version](https://poser.pugx.org/insenseanalytics/laravel-nova-permission/version.png)](https://packagist.org/packages/insenseanalytics/laravel-nova-permission)
[![Total Downloads](https://poser.pugx.org/insenseanalytics/laravel-nova-permission/d/total.png)](https://packagist.org/packages/insenseanalytics/laravel-nova-permission)

This [Nova](https://nova.laravel.com) tool lets you:
- manage roles and permissions on the Nova dashboard
- use permissions based authorization for Nova resources

## Nova Dashboard Screenshots

## Requirements & Dependencies
There are no PHP dependencies except the Laravel Nova package and the Spatie Permission library.

## Installation
You can install this tool into a Laravel app that uses [Nova](https://nova.laravel.com) via composer:

```bash
composer require insenseanalytics/laravel-nova-permission
```

Next, if you do not have package discovery enabled, you need to register the provider in the `config/app.php` file.
```php
'providers' => [
    ...,
    Insenseanalytics\LaravelNovaPermission\NovaPermissionServiceProvider::class,
]
```

Next, you must register the tool with Nova. This is typically done in the `tools` method of the `NovaServiceProvider`.

```php
// in app/Providers/NovaServiceProvider.php

public function tools()
{
    return [
        // ...
        \Insenseanalytics\LaravelNovaPermission\LaravelNovaPermission::make(),
    ];
}
```

Finally, add `MorphToMany` fields to you `app/Nova/User` resource:

```php
use Laravel\Nova\Fields\MorphToMany;

public function fields(Request $request)
{
    return [
        // ...
        MorphToMany::make('Roles', 'roles', \InsenseAnalytics\LaravelNovaPermission\Role::class),
        MorphToMany::make('Permissions', 'permissions', \InsenseAnalytics\LaravelNovaPermission\Permission::class),
    ];
}
```

## Using Custom Role/Permission Resource Classes

If you want to use custom resource classes you can define them when you register a tool:

```php
// in app/Providers/NovaServiceProvider.php

public function tools()
{
    return [
        // ...
        \Insenseanalytics\LaravelNovaPermission\LaravelNovaPermission::make()
            ->roleResource(CustomRole::class)
            ->permissionResource(CustomPermission::class),
    ];
}
```

## Permissions Based Authorization for Nova Resources
By default, Laravel Nova uses Policy based authorization for Nova resources. If you are using the Spatie Permission library, it is very likely that you would want to swap this out to permission based authorization without the need to define Authorization policies.

To do so, you can use the `PermissionsBasedAuthTrait` and define a `permissionsForAbilities` static array property in your Nova resource class like so:

```php
// in app/Nova/YourNovaResource.php

class YourNovaResource extends Resource
{
    use \Insenseanalytics\LaravelNovaPermission\PermissionsBasedAuthTrait;

    public static $permissionsForAbilities = [
      'all' => 'manage products',
    ];
}
```

The example above means that all actions on this resource can be performed by users who have the "manage products" permission. You can also define separate permissions for each action like so:

```php
    public static $permissionsForAbilities = [
      'viewAny' => 'view products',
      'view' => 'view products',
      'create' => 'create products',
      'update' => 'update products',
      'delete' => 'delete products',
      'restore' => 'restore products',
      'forceDelete' => 'forceDelete products',
      'addAttribute' => 'add product attributes',
      'attachAttribute' => 'attach product attributes',
      'detachAttribute' => 'detach product attributes',
    ];
```

## Contributing

Contributions are welcome and will be fully credited as long as you use PSR-2, explain the issue/feature that you want to solve/add and back your code up with tests. Happy coding!

## License

The MIT License (MIT). Please see [License File](LICENSE.txt) for more information.