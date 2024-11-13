## Laravel Project Setup

### Prerequisites
* php is installed and updated
* composer is installed and updated
* a server is installed (apache or nginx usually. ```php artisan serve``` is a built in server, but it is not suitable as a development environment)
* a database is installed (postgresql. laravel uses mysql by default)

### Procedure
* 
* Build the project, use command: ```laravel new project-name-here``` OR ```composer create-project laravel/laravel project-name-here``` if you need to specify the version of laravel. (laravel new is better)
* Install dependencies/create lock files, use command: ```composer install``` and ```npm install```
* Ensure Web Server is running
* * if using wsl2, always manually start apache server: ```sudo service apache2 start```
* * true linux systems can be configured to have apache start when linux starts (ie. always running)
* Ensure Database Server is running
* * if using wsl2, always manually start postgresql server: ```sudo service postgresql start```
* * true linux systems can be configured to have postgresql start when linux starts (ie. always running)
* Create Database (no tables/schemas)
* * The Database Name, Username, and Password should created at this point
* * postgresql username and password should have been setup after/during installation of postgresql
* Connect the database
* * update the project's .env file (make sure it is git ignored)
* * to determine postgresql server port, use command: ```sudo -u postgres psql -c "SHOW port;"```
* * during development, the postgresql server host is 127.0.0.1 (localhost)
* * during deployment, the postgresql server host needs to be updated
* Setup the database tables/relations
* * Create Models/Migration files, use command: ```php artisan make:model ModelNameHere -m``` (-m makes a migration along side the model)
* * Run Migration Files (will not work if db is not connected properly), use command: ```php artisan migrate```

