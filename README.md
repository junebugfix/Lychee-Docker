[![Build Status][build-status-shield]](https://travis-ci.com/LycheeOrg/Lychee-Docker)
[![Last Commit][last-commit-shield]](https://github.com/LycheeOrg/Lychee-Docker/commits/master)
[![Release][release-shield]](https://github.com/LycheeOrg/Lychee-Docker/releases)
[![Lychee Version][lychee-version-shield]](https://hub.docker.com/r/lycheeorg/lychee)
[![Docker Pulls (new)][docker-pulls-shield]](https://hub.docker.com/r/lycheeorg/lychee)
[![Docker Pulls (old)][docker-pulls-shield-old]](https://hub.docker.com/r/lycheeorg/lychee-laravel)
<br>
![Supports amd64 Architecture][amd64-shield]
![Supports arm64/aarch64 Architecture][arm64-shield]
![Supports armv6 Architecture][armv6-shield]
![Supports armv7 Architecture][armv7-shield]

## Notice: Dockerhub repository has been migrated to [lycheeorg/lychee](https://hub.docker.com/r/lycheeorg/lychee)  
**Make sure you update your docker-compose files accordingly**

## Table of Contents
<!-- TOC depthFrom:1 depthTo:6 withLinks:1 updateOnSave:1 orderedList:0 -->
- [Image content](#image-content)
- [Setup](#setup)
	- [Prerequisites](#prerequisites)
	- [Run with Docker](#run-with-docker)
	- [Run with Docker Compose](#run-with-docker-compose)
- [Available environment variables and defaults](#available-environment-variables-and-defaults)
- [Advanced configuration](#advanced-configuration)
<!-- /TOC -->

## Image Content

This image features Lychee, nginx and PHP-FPM. The provided configuration (PHP, nginx...) follows Lychee's official recommendations.

The following tags are available :

* `latest`: Latest Lychee release
* `v[NUMBER]`: Stable version tag for a Lychee release
* `dev`: Current master branch tag (Lychee operates on a stable master, so this should usually be safe)
* `testing`: Tag for testing new branches and pull requests. Designed for internal use by LycheeOrg.

Note that only the `:dev` tag is available for armv6 and armv7 systems. This is due to an issue with the build environment and is hopefully temporary.

## Setup

### Prerequisites

You must have a database docker running **OR** create one in your `docker-compose.yml`.

1.  Create the db, username, password.
2.  Edit the environment variables (db credentials, language...) by :
    *  Supplying the environment variables via `docker run` / `docker-compose`, **or**
    *  Creating a `.env` file with the appropriate info and mount it to `/conf/.env`.

### Run with Docker

**Make sure that you link to the container running your database !!**  

The example below shows `--net` and `--link` for these purposes. `--net` connects to the name of the network your database is on and  `--link` connects to the database container.

```bash
docker run -d \
--name=lychee \
-v /host_path/lychee/conf:/conf \
-v /host_path/lychee/uploads:/uploads \
-v /host_path/lychee/sym:/sym \
-e PUID=1000 \
-e PGID=1000 \
-e PHP_TZ=America/New_York \
-e DB_CONNECTION=mysql \
-e DB_HOST=mariadb \
-e DB_PORT=3306 \
-e DB_DATABASE=lychee \
-e DB_USERNAME=user \
-e DB_PASSWORD=password \
-p 90:80 \
--net network_name \
--link db_name \
lycheeorg/lychee
```

**Warning** : if you use a MySQL database, make sure to use the `mysql_native_password` authentication plugin, either by using the `--default-authentication-plugin` option when starting mysql, or by running a query to enable the authentication plugin for the `lychee` user, e.g. :

```
alter user 'lychee' identified with mysql_native_password by '<your password>';
```

### Run with Docker Compose

Change the environment variables in the [provided example](./docker-compose.yml) to reflect your database credentials.

Note that in order to avoid writing credentials directly into the file, you can create a `db_secrets.env` and use the `env_file` directive (see the [docs](https://docs.docker.com/compose/environment-variables/#the-env_file-configuration-option)).

## Available environment variables and defaults

If you do not provide environment variables or `.env` file, the [example .env file](https://github.com/LycheeOrg/Lychee/blob/master/.env.example) will be used with some values already set by default.

Some variables are specific to Docker, and the default values are :

* `PUID=1000`
* `PGID=1000`
* `USER=lychee`
* `PHP_TZ=UTC`
* `STARTUP_DELAY=0`

## Advanced configuration

Note that nginx will accept by default images up to 100MB (`client_max_body_size 100M`) and that PHP parameters are overridden according to the [recommendations of the Lychee FAQ](https://lycheeorg.github.io/docs/faq.html#i-cant-upload-photos).

You may still want to further customize PHP configuration. The first method is to mount a custom `php.ini` to `/etc/php/7.3/fpm/php.ini` when starting the container. However, this method is kind of brutal as it will override all parameters.

Instead, we recommend to use the `PHP_VALUE` directive of PHP-FPM to override specific parameters. To do so, you will need to mount a custom `nginx.conf` in your container :

1. Take the [default.conf](./default.conf) file as a base
2. Find the line starting by `fastcgi_param PHP_VALUE [...]`
3. Add a new line and set your new parameter
4. Mount your new file to `/etc/nginx/nginx.conf`

[arm64-shield]: https://img.shields.io/badge/arm64-yes-success.svg?style=flat
[amd64-shield]: https://img.shields.io/badge/amd64-yes-success.svg?style=flat
[armv6-shield]: https://img.shields.io/badge/armv6-partial-yellow.svg?style=flat
[armv7-shield]: https://img.shields.io/badge/armv7-partial-yellow.svg?style=flat
[build-status-shield]: https://img.shields.io/travis/com/LycheeOrg/Lychee-Docker/master.svg?style=flat
[docker-pulls-shield-old]: https://img.shields.io/docker/pulls/lycheeorg/lychee-laravel.svg?style=flat&label=Docker%20Pulls%20(lychee-laravel)
[docker-pulls-shield]: https://img.shields.io/docker/pulls/lycheeorg/lychee.svg?style=flat&label=Docker%20Pulls%20(lychee)
[lychee-version-shield]: https://img.shields.io/docker/v/lycheeorg/lychee/latest?style=flat&label=Lychee%20Version%20(:latest)
[last-commit-shield]: https://img.shields.io/github/last-commit/LycheeOrg/Lychee-Docker.svg?style=flat
[release-shield]: https://img.shields.io/github/release/LycheeOrg/Lychee-Docker.svg?style=flat
