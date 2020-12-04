# Deploy Taiga with Docker

## Requirements

- docker
- docker-compose

Additionally, it's necessary to have familiarity with Docker, docker-compose and Docker repositories.

## Simple customization

There are some environment variables for a simple customization. Find them in the `docker-compose.yml`. The images are ready to work out of the box, although is strongly recommended to change some default values.


### taiga-db

##### `POSTGRES_DB`, `POSTGRES_USER`, `POSTGRES_PASSWORD`

This vars will be used to create the database for Taiga.

**Important**: these vars should have the same values as `taiga-back` vars.


### taiga-back and taiga-async

#### Database settings

##### `POSTGRES_DB`, `POSTGRES_USER`, `POSTGRES_PASSWORD`

This vars will be used to connect to the Taiga database.

**Important**: these vars should have the same values as `taiga-db` service vars.

##### `POSTGRES_HOST` 

Where the database is set. By default, it's meant to be in the same host as the database service so it uses internal docker names.

#### Taiga settings

##### `TAIGA_SECRET_KEY` 

Is the secret key of Taiga. Should be the same as this var in `taiga-events` and `taiga-async`

##### `TAIGA_SITES_SCHEME`, `TAIGA_SITES_DOMAIN`

Should have the url where this is served: https[://]taiga.mycompany.com

#### Email Settings

By default, email is configured with the *console* backend, which means that the emails will be shown in the stdout.<br>

##### `ENABLE_EMAIL`

If you have an smtp service, configure this variable to "True". By default is "False".

##### `DEFAULT_FROM_EMAIL`, `EMAIL_HOST`, `EMAIL_PORT`, `EMAIL_HOST_USER`, `EMAIL_HOST_PASSWORD`

If you have an smtp service configure this variables too.

##### `EMAIL_USE_TLS`

If you use TLS, configure this variable to "True". By default is "False".

##### `EMAIL_USE_SSL`

If you use SSL, configure this variable to "True". By default is "False".

#### Rabbit settings

##### `RABBITMQ_USER`, `RABBITMQ_PASS`

Are used to leave messages in the rabbitmq services. Those variables should be the same as in `taiga-async-rabbitmq` and `taiga-events-rabbitmq`.

#### Github settings

##### `GITHUB_API_CLIENT_ID`, `GITHUB_API_CLIENT_SECRET`

Used for login with Github.
Get these in your profile https://github.com/settings/apps or in your organization profile https://github.com/organizations/{ORGANIZATION-SLUG}/settings/applications

#### Gitlab settings

##### `GITLAB_API_CLIENT_ID`, `GITLAB_API_CLIENT_SECRET`, `GITLAB_URL`

Used for login with GitLab.
Get these in your profile https://{YOUR-GITLAB}/profile/applications or in your organization profile https://{YOUR-GITLAB}/admin/applications


### taiga-async-rabbitmq

##### `RABBITMQ_ERLANG_COOKIE`

Is the secret erlang cookie.

##### `RABBITMQ_DEFAULT_USER`, `RABBITMQ_DEFAULT_PASS`, `RABBITMQ_DEFAULT_VHOST`

This vars will be used to connect to rabbitmq.


### taiga-front

##### `TAIGA_URL` 

Where this Taiga instance should be served. It should be the same as `TAIGA_SITES_SCHEME`://`TAIGA_SITES_DOMAIN`.

##### `TAIGA_WEBSOCKETS_URL` 

To connect to the events. This should have the same value as `TAIGA_SITES_DOMAIN`, ie: ws://taiga.mycompany.com

##### `GITHUB_CLIENT_ID`

Used for login with Github.
Get these in your profile https://github.com/settings/apps or in your organization profile https://github.com/organizations/{ORGANIZATION-SLUG}/settings/applications

##### `GITLAB_CLIENT_ID`, `GITLAB_URL`

Used for login with GitLab.
Get these in your profile https://{YOUR-GITLAB}/profile/applications or in your organization profile https://{YOUR-GITLAB}/admin/applications


### taiga-events

##### `RABBITMQ_USER`, `RABBITMQ_PASS`

Are used to read messages from rabbitmq

##### `TAIGA_SECRET_KEY`

Should be the same as this var in `taiga-back`


### taiga-events-rabbitmq

##### `RABBITMQ_ERLANG_COOKIE`

Is the secret erlang cookie.

##### `RABBITMQ_DEFAULT_USER`, `RABBITMQ_DEFAULT_PASS`, `RABBITMQ_DEFAULT_VHOST`

This vars will be used to connect to rabbitmq.


## Storage

We have 3 named volumes configured: `taiga-static-data` for statics, `taiga-media-data` for medias and `taiga-db-data` for the database.


## Complex customization

For a complex customization, you can use configuration files, mapped to specific directories inside the containers.

### taiga-back

Map a Python configuration file to `/taiga-back/settings/config.py`. You can use (this file)[https://github.com/taigaio/taiga-back/blob/taiga-6/docker/config.py] as an example.

**Important**: if you use your own configuration file, don't forget to add contribs to `INSTALLED_APPS` (check the example `config.py`).

### taiga-front

Map a `conf.json`configuration file to `/usr/share/nginx/html/conf.json`. You can use (this file)[https://github.com/taigaio/taiga-front/blob/taiga-6/docker/conf.json.template] as an example.


## Before running

You have to configure an admin user:
```sh
# ensure migrations are properly set
$ docker-compose up -d

$ docker-compose -f docker-compose.yml -f docker-compose-inits.yml run --rm taiga-manage createsuperuser
```

## Up and running

```sh
$ docker-compose up -d
```

Default access for the application is **http://localhost:9000**.


## One shot commands

`docker-compose-inits.yml` lets launch manage.py commands on the back instance:

```sh
$ docker-compose -f docker-compose.yml -f docker-compose-inits.yml run --rm taiga-manage [COMMAND]
```