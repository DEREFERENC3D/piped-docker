# Docker Compose configuration for Piped

This repository contains a compose file for running an instance of Piped, based on the [official version](https://github.com/TeamPiped/Piped-Docker). Changes from upstream:

* Removed reverse proxy configs,
* Moved configuration to `env_files`, removed the configure script.

# Running - configuration

All the services need to communicate with some of the other ones, and for that they need their addresses / URLs. This is the one thing that needs setting in **every** deployment.

In addition to that, the backend has a few optional settings, but the defaults should work fine.

### Backend

The configuration for the backend is stored in `config.properties`. Make sure to set the URLs of the various services:

* `PROXY_PART`,
* `API_URL`,
* `FRONTEND_URL`.

### Frontend

The frontend is only configured using environment variables. The compose sets up the container to read them from `frontend.env` (default config) and `frontend.env.local` - instance-specific config, for you to create and override the default values with.

Once again, the only required change is the hostname of the backend server (`BACKEND_HOSTNAME`). If the backend server should be contacted over HTTP (rather than HTTPS), also set `HTTP_MODE=http`.

### Postgres (optional)

Just like the frontend service, this one is configurable with environment files, `postgres.env` and `postgres.env.local`. This allows to change the DB name, username and password. This step is not required as there is a default username and password set and using them is arguably not insecure as the DB server should not be reachable from outside the containers' internal network.

If you do change them, don't forget to also reflect the change in `config.properties` (DB name at the end of `hibernate.connection.url`, username & password in `hibernate.connection.username` & `hibernate.connection.password`), otherwise the backend will attempt to use wrong credentials when communicating with the DB.
