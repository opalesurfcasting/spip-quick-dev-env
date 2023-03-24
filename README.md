# Reminder for quick [Spip](https://www.spip.net/) local dev environment setup with docker, spip-cli and php

Prerequisites :
- docker, composer, php (with update-alternatives if needed), spip-cli

Docs :
- https://contrib.spip.net/SPIP-Cli


In one console :
## Run docker mysql container
`docker run --name db -e MYSQL_ROOT_PASSWORD=1234 -p 33060:3306 -v $(pwd)/db-vol:/var/lib/mysql -d mysql:5.7 --character-set-server=utf8 --collation-server=utf8_unicode_ci`

## Run logs for mysql db container
`docker logs db -f`

In another console :
## Run docker adminer and connect to mysql db container

`docker run --name adminer --link db -p 8080:8080 -d adminer:latest`

## Run logs for adminer container

`docker logs adminer -f`

In a third console :

## Create spip site from spip-cli

### download spip files with wanted version
`spip dl --branche spip-3.2.19`
### Create needed directories
`spip core:preparer`
### Connect to db and create config files
`spip core:installer --db-server=mysql --db-login=thelogin --db-pass=thepass --db-host=the.docker.db.ip`

## (if needed) Upload original Spip website dataset to mysql db container 
`mysql -u thelogin -P 3306 -h the.docker.db.ip -p spip < ~/way-to/dump.sql`

## Update original spip site host to local dev host :
`spip config:ecrire -f tmp/adresse_site.json`

## Create original plugins list from original spip site dataset
`spip plugins:lister -e`

## Install plugins from original plugins list (some plugins may not be available and will need a manual installation from spip admin)
`spip plugins:activer --from-file=tmp/plugins.txt` or `spip plugins:activer -e`

## Clear Spip caches
`spip cache:vider`

## Run the built-in web server
`php -S localhost:8000`

or :

## Run the built-in web server with the built-in PHP server and Xdebug (debug in phpstorm)
`php -S localhost:8000 -d xdebug.mode=debug -d xdebug.client_host:localhost -d xdebug.client_port:9003 -d xdebug.discover_client_host`

## Go to http://localhost:8000

Sources : 
- https://contrib.spip.net/SPIP-Cli
- https://hub.docker.com/r/ipeos/spip/#!
- https://www.spip.net/fr_rubrique91.html
- https://git.spip.net/spip-contrib-outils/spip-cli

- https://www.spip.net/fr_article884.html
