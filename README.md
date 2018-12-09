# WordPress Development

A WordPress development environment, using [docker](https://www.docker.com) (via [docker-compose](https://docs.docker.com/compose/)), complete with [WordPress](https://wordpress.org), [PHP FPM](https://php-fpm.org), [MariaDB](https://mariadb.org), [Nginx](https://nginx.org), [composer](https://getcomposer.org), [WP-CLI](https://wp-cli.org), [npm](https://www.npmjs.com) and [git](https://git-scm.com) services.

## Installation

- Create a new directory for your project: `mkdir my-wordpress-project`
- Change into this directory: `cd my-wordpress-project`
- Clone this repository: `git clone https://github.com/GrottoPress/wordpress-dev.git .`
- Copy the `.sample.env` file to `.env`: `cp .sample.env .env`
- Set permissions for the `.env` file: `chmod 0600 .env`
- Edit the variables in your new `.env` file to taste
- Run `docker-compose up -d` to start the services
- Visit `http://<NGX_HOST>:<NGX_PORT>` (use the host and port you set in `.env`) in your browser to complete WordPress' installation

## Environment Variables

The following variables are available for use in your `.env` file:

| Variable            | Description                          |
|---------------------|--------------------------------------|
| `DB_ROOT_PASS`      | Database root user password          |
| `DB_NAME`           | Database name                        |
| `DB_USER`           | Database user                        |
| `DB_PASS`           | Database user password               |
| `NGX_HOST`          | Hostname of virtual host             |
| `NGX_PORT`          | The port on the host machine to bind the nginx container's port to. Determines the port to access the WordPress installation on the host machine |
| `PHP_VERSION`       | Format: `<major>.<minor>`. Versions `7.0` to `7.2` supported |
| `WP_VERSION`        | Format: `<major>.<minor>`. Versions `4.7` to `5.0` supported |
| `WP_DEBUG`          |  |
| `WP_TABLE_PREFIX`   |  |
| `WP_AUTH_KEY`       |  |
| `WP_LOGGED_IN_KEY`  |  |
| `WP_AUTH_SALT`      |  |
| `WP_LOGGED_IN_SALT` |  |
| `WORK_DIR`          | Directory of the particular plugin or theme you are working on. Sets the context for composer, npm and git commands. Eg: `wp-content/plugins/my-plugin` |

## Get to Work

The `wp-content` directory of the WordPress installation corresponds to the the `./app` directory of this setup.

Therefore, if developing a theme, it goes into the `./app/themes` directory. Plugins go into the `./app/plugins` directory.

You should have set the `WORK_DIR` environment variable to the directory of the theme or plugin you are working on.

This way, you do not need to `cd` into that directory to issue `git`, `npm` or `composer` commands.

### Using WP-CLI

To run the WP-CLI service, run `docker-compose run --rm wp-cli wp`.

You may alias the `wp` command to this, thus: `alias wp="docker-compose run --rm wp-cli wp"`. You may, then, use `wp` instead of typing the entire command.

Eg: `wp core version`

Add the alias command in your `~/.bashrc` file, if you want to set this permanently.

### Using Composer

To run the Composer service, run `docker-compose run --rm composer`.

You may alias the `composer` command to this, thus: `alias composer="docker-compose run --rm composer"`. You may, then, use `composer` instead of typing the entire command.

Eg: `composer update`

Add the alias in your `~/.bashrc` file, if you want to set this permanently.

### Using NPM

To run NPM, run: `docker-compose run --rm node npm`.

You may alias the `npm` command to this, thus: `alias npm="docker-compose run --rm node npm"`. You may, then, use `npm` instead of typing the entire command.

Eg: `npm install`

Add the alias in your `~/.bashrc` file, if you want to set this permanently.

### Using Git

To run git, run: `docker-compose run --rm git`.

You may alias the `git` command to this, thus: `alias git="docker-compose run --rm git"`. You may, then, use `git` instead of typing the entire command.

Eg: `git status`

Add the alias in your `~/.bashrc` file, if you want to set this permanently.

## Useful commands

- Start containers: `docker-compose up -d`
- Stop containers: `docker-compose down`
- Check logs: `docker-compose logs <service>`. Eg: `docker-compose logs nginx`
- Access shell prompt: `docker-compose exec <service> /bin/sh`. Eg: `docker-compose exec nginx /bin/sh`

## Notes on Changing WordPress version

Currently, the entire WordPress installation is persisted in a named volume (`app`), to ensure the other services (eg: `nginx`) have access to them. (This is not ideal; any ideas are welcome.)

Therefore, if you change the WordPress version, you need to remove the corresponding volume (`wordpress_app`) with the command `docker volume rm wordpress_app`, before running `docker-compose up -d` again.

Remember to stop containers, with `docker-compose down`, before removing volume.
