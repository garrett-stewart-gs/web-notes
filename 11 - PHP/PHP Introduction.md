## PHP Introduction

### What is it?
* scripting language
* dynamic typing (not type safe)
* runs on web server (commonly Apache or Nginx)
* must be installed prior to laravel (or comparable framework)

### Conventions
* snake_case

### Installation
* You must install the php interpreter to run php
```
sudo apt update
sudo apt install php
```

### Running Code
* After installation, you can invoke the php interpreter and pass it the php file you wish to execute
```
php script.php
```

### Starting Built-In Server (can be replaced with apache or nginx)
* You can start a server to run a web application
```
php -S localhost:3000
```

### Package Manager: Composer
* Composer is PHP's package manager (same as npm for node)
* PHP Projects have a "composer.json" file (same as package.json for node)
* PHP Projects have a "composer.lock" file (same as package-lock.json for node)
* You can issue package management commands:
```
composer install // aka npm install
composer update // aka npm update
```
* You can create a laravel package
```
composer create-project laravel/laravel my-project
```