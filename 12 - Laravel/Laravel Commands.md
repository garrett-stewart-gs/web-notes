## Project Generation
* ```laravel new <project name>``` // creates project structure and prompts user for installation options
* ```composer create-project laravel/laravel <project name>``` // same as laravel new command, but slower. Useful for specifying laravel version

## PHP Artisan Commands

### Make
####
```php artisan make:model ModelNameHere -m``` // generates model and migration (for that table?)
```php artisan make:controller/model```

### Middleware
```php artisan middleware```


### Migrate
```php artisan migrate``` // runs all migrations (ie updates database)
```php artisan migrate:fresh```
