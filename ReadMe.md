## Docker environment for Symfony 5 application

A basic docker environment for Symfony 5 application on PHP 7.4

#### Requirements:

* Docker latest version up & running

#### Usage:

* I will name our app `symfonyapp.local`, so we 
need to update our `/etc/hosts` file:
```bash
$ sudo vim /etc/hosts
```
Then add `127.0.0.1       symfonytest.local`.

* Now am not making docker install Symfony for you so
you can install whatever version you like, will install `5.1.*`
and I will install it in a folder called `app`:
```bash
$ composer create-project symfony/website-skeleton app ^5.1.0
```

* You need to take a copy from `.env.dist` into another file
with name `.env` and in this file you can put all your env
variables you might need to use away from the default Symfony
`.env` file. It only contains DB configs for now.

* Go to `app/.env` and comment the line starts with `DATABASE_URL=` (I think it is line #32). 
We are doing that as we already defined it [here](docker-compose.yml#23).

* Now, bring the containers up: (make sure docker is running first)
```bash
$ docker-compose up -d --build
``` 

* Check the containers running:
```bash
$ docker ps -a
CONTAINER ID        IMAGE                              COMMAND                  CREATED             STATUS              PORTS                                      NAMES
21dc85912175        symfony_docker_template_nginx      "/docker-entrypoint.…"   4 minutes ago       Up 4 minutes        0.0.0.0:80->80/tcp, 0.0.0.0:443->443/tcp   symfony_docker_template_nginx_1
c90e27a97476        symfony_docker_template_php        "docker-php-entrypoi…"   4 minutes ago       Up 4 minutes        9000/tcp                                   symfony_docker_template_php_1
2e194a46ac8c        symfony_docker_template_database   "docker-entrypoint.s…"   4 minutes ago       Up 4 minutes        3306/tcp                                   symfony_docker_template_database_1
```

* Navigate to http://symfonyapp.local and you should see Symfony
default home page.
> Note: you might get a `502` error for a couple of seconds.
>This happens as the PHP-fpm takes some time until it is up as well as the 
>composer install command takes some time until it finishes.
>You can check the log `$ docker logs c90e27a97476 -f`

**Congrats!** you can start developing now :tada::tada:

----------

#### Configure custom project/folders naming
For sure you can change the name from `symfonyapp.local` to whatever 
you want, you can also change the project folder name from app to anything else
as well as change the project folder name on the containers 
but you have to do some changes in some places as well:

* [docker-compose:php:volume](docker-compose.yml#L18)
* [docker-compose:nginx:volume](docker-compose.yml#L30)
* [docker-containers:php-fpm:Dockerfile](docker-containers/php-fpm/Dockerfile#L32)
* [docker-containers:php-fpm:ini-file](docker-containers/php-fpm/php-ini-local.ini#L6)
* [docker-containers:nginx:Dockerfile](docker-containers/nginx/Dockerfile#L7)
* [docker-containers:nginx:sites:default](docker-containers/nginx/sites/default.conf#L5,#L6)

------

#### Some helpful commands:

* Check specific container logs:
```bash
$ docker logs c90e27a97476 -f
```

* Run specific command on the Symfony project, composer install package:
```bash
$ docker-compose exec php composer require --dev symfony/phpunit-bridge
```
As you see we do `docker-compose exec php` then add whatever command you want.

* Clear the cache for example:
```bash
$ docker-compose exec php bin/console cache:clear -e dev
```

* Install the [webpack](https://symfony.com/doc/current/frontend.html) packages:
```bash
$ docker-compose exec php yarn install
```

* Add a specific package to your webpack:
```bash
$ docker-compose exec php yarn add bootstrap --dev
```

* Bring all containers down:
```bash
$ docker-compose down
```

Enjoy developing! 👨‍💻 👩‍💻

**Contributions are more than welcome.**
