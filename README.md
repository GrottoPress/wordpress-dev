# WordPress Development

A WordPress development environment, using [docker](https://www.docker.com) (via [docker-compose](https://docs.docker.com/compose/)), complete with [WordPress](https://wordpress.org), [PHP FPM](https://php-fpm.org), [MariaDB](https://mariadb.org), [Nginx](https://nginx.org), [composer](https://getcomposer.org), [WP-CLI](https://wp-cli.org), [npm](https://www.npmjs.com) and [git](https://git-scm.com) services.

## Installation

- Create a new directory for your project: `mkdir my-wordpress-project`
- Change into this directory: `cd my-wordpress-project`
- Clone this repository: `git clone https://github.com/GrottoPress/wordpress-dev.git ../wp-dev`
- Copy `src/` files into your new project's directory: `cp -rf ../wp-dev/src/. .`
- Copy the `sample.env` file to `.env`: `cp sample.env .env`
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
| `PHP_VERSION`       | Format: `<major>.<minor>`. |
| `WP_VERSION`        | Format: `<major>.<minor>`. |
| `WP_DEBUG`          |  |
| `WP_TABLE_PREFIX`   |  |
| `WP_AUTH_KEY`       |  |
| `WP_LOGGED_IN_KEY`  |  |
| `WP_AUTH_SALT`      |  |
| `WP_LOGGED_IN_SALT` |  |
| `WORK_DIR`          | Directory of the particular plugin or theme you are working on. Sets the context for composer, npm and git commands. Eg: `wp-content/plugins/my-plugin` |

## Get to Work

The `wp-content` directory of the WordPress installation corresponds to the the `app/` directory of your project.

Therefore, if developing a theme, it goes into the `app/themes/` directory. Plugins go into the `app/plugins/` directory.

You should have set the `WORK_DIR` environment variable to the directory of the theme or plugin you are working on.

This way, you do not need to `cd` into that directory to issue `git`, `npm` or `composer` commands.

## Using Commands

Copy and paste the following into your `~/.bashrc` file.

```bash
# BEGIN: Docker/compose
#

docker_compose_git()
{
    if [ -f "${PWD}/docker-compose.yml" ]; then
        docker-compose run --rm git "$@"
    else
        $(which git) "$@"
    fi
}

docker_compose_composer()
{
    if [ -f "${PWD}/docker-compose.yml" ]; then
        docker-compose run --rm composer "$@"
    else
        $(which composer) "$@"
    fi
}

docker_compose_wp()
{
    if [ -f "${PWD}/docker-compose.yml" ]; then
        docker-compose run --rm wp-cli wp "$@"
    else
        $(which wp) "$@"
    fi
}

docker_compose_npm()
{
    if [ -f "${PWD}/docker-compose.yml" ]; then
        docker-compose run --rm node npm "$@"
    else
        $(which npm) "$@"
    fi
}

alias wp="docker_compose_wp"
alias composer="docker_compose_composer"
alias npm="docker_compose_npm"
alias git="docker_compose_git"

#
# END: Docker/Compose
```
Run `source ~/.bashrc` or restart terminal, to load the new configuration.

You may then use `wp`, `composer`, `npm` and `git` commands as you would normally.

## Useful commands

- Start containers: `docker-compose up -d`
- Stop containers: `docker-compose down`
- Check container logs: `docker-compose logs <service>`. Eg: `docker-compose logs nginx`
- Access container shell prompt: `docker-compose exec <service> /bin/sh`. Eg: `docker-compose exec nginx /bin/sh`

## Notes on Changing WordPress version

Currently, the entire WordPress installation is persisted in a named volume (`app`), to ensure the other services (eg: `nginx`) have access to them. (This is not ideal; any ideas are welcome.)

Therefore, if you change the WordPress version, you need to remove the corresponding volume (`wordpress_app`) with the command `docker volume rm wordpress_app`, before running `docker-compose up -d` again.

Remember to stop containers, with `docker-compose down`, before removing volume.
