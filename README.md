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

Once again, the only required change is the URLs, but in this case of all services:

* `BASE_URL` should point to itself (the `frontend` service),
	* For example, for a reverse proxy server on running on `https://kavin.rocks` with a route `/piped/` proxying the request to the `frontend` container, set `BASE_URL=https://kavin.rocks/piped`
	* For a reverse proxy sending all requests for domain `piped.kavin.rocks` to the container set `BASE_URL=https://piped.kavin.rocks`
* `BACKEND_URL` should point to the `piped` (backend) service, as above, full URLs are required and the service may be accessible on a subpath (e.g. `/piped-backend`) rather than on the root (`/`),
* `PROXY_URL` should point to the `proxy` service,
* (optional) `INSTANCES_URL` should point to a service providing the list of public instances (not included in the compose, default should work fine).

### Postgres (optional)

Just like the frontend service, this one is configurable with environment files, `postgres.env` and `postgres.env.local`. This allows to change the DB name, username and password. This step is not required as there is a default username and password set and using them is arguably not insecure as the DB server should not be reachable from outside the containers' internal network.

If you do change them, don't forget to also reflect the change in `config.properties` (DB name at the end of `hibernate.connection.url`, username & password in `hibernate.connection.username` & `hibernate.connection.password`), otherwise the backend will attempt to use wrong credentials when communicating with the DB.
