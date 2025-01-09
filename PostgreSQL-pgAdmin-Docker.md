# Dockerized PostgreSQL with PgAdmin
## FIRST
Install Docker and start it

## PostgreSQL & Pg Admin Setup
### docker-compose.yml file
- Create file `docker-compose.yml` in the root of your server folder
```yml
services:

  postgres:
    container_name: database
    image: postgres:latest
    ports:
      - "5432:5432"
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
      POSTGRES_DB: test_db
    volumes:
      - ./data/database:/var/lib/postgresql/data
    restart: unless-stopped

  pgadmin:
    container_name: pgadmin
    image: dpage/pgadmin4
    depends_on:
      - postgres
    ports:
      - "5050:80"
    environment:
      - PGADMIN_DEFAULT_EMAIL=pg@pg.com
      - PGADMIN_DEFAULT_PASSWORD=pg
    volumes:
      - ./data/pgadmin:/var/lib/pgadmin
    restart: unless-stopped

```
Run `docker compose up` in the `server` folder
- Check that the `pgadmin` works: go to `localhost:5050` with your browser and log in with the credentials
- Check that `database` works -> Right-click on the `Servers` -> `Register` -> `Server...` -> Give a name for your server -> Go to the `Connection` tab -> Host name = `postgres`, Port = `5432`, Maintenance database = `postgres`, and Username and Password are the values from the `docker-compose.yml` file -> Click `Save` and you should have your database running

### Add the enviroment variables to .env file
`.env` file:
```
# Port
PORT = 3000

# Database
DB_NAME = test_db
DB_PORT = 5432
DB_HOST = postgres
DB_MAINTENANCE = postgres

# PostgreSQL
POSTGRES_USER = postgres
POSTGRES_PASSWORD = postgres

# Pg Admin
PGADMIN_DEFAULT_EMAIL = pg@pg.com
PGADMIN_DEFAULT_PASSWORD = pg
PG_PORT = 5050
```

`docker-compose.yml` file:
```yml
...
  postgres:
...
    ports:
      - "${DB_PORT}:${DB_PORT}"
    environment:
      POSTGRES_USER: ${POSTGRES_USER}
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
      POSTGRES_DB: ${DB_NAME}
...
  pgadmin:
...
    ports:
      - "${PG_PORT}:80"
    environment:
      - PGADMIN_DEFAULT_EMAIL=${PGADMIN_DEFAULT_EMAIL}
      - PGADMIN_DEFAULT_PASSWORD=${PGADMIN_DEFAULT_PASSWORD}
...

```
### Auto server setup
Create `servers.json` file in your `utils/db` folder:
```json
{
	"Servers": {
		"1": {
			"Name": "test_db",
			"Group": "Servers",
			"Host": "postgres",
			"Port": 5432,
			"MaintenanceDB": "postgres",
			"Username": "postgres",
			"Password": "postgres",
			"SSLMode": "prefer"
		}
	}
}
```
- Update pgdmin volumes in `docker-compose.yml`:
```yml
services:
...
  pgadmin:
...
    volumes:
      - ./src/utils/db/config/servers.json:/pgadmin4/servers.json
      - ./data/pgadmin:/var/lib/pgadmin
...

```
- Give permissons to use the file `chmod 644 ./src/utils/db/config/servers.json`
- Run `docker compose up` and check in your pgAdmin that the database is created (localhost:5050)

#### Use .env variables in the servers.json file
Create `servers.template.json` file in the same folder where your `servers.json` is:
```json
{
	"Servers": {
		"1": {
			"Name": "${DB_NAME}",
			"Group": "Servers",
			"Host": "{DB_HOST}",
			"Port": 5432,
			"MaintenanceDB": "${DB_MAINTENANCE}",
			"Username": "${POSTGRES_USER}",
			"Password": "${POSTGRES_PASSWORD}",
			"SSLMode": "prefer",
		}
	}
}
```
- Add `**/servers.json` to your `.gitignore` file or just remove it
- Create `Dockerfile.pgadmin` file in the same folder where your `docker-compose.yml` file is:
```Dockerfile
FROM dpage/pgadmin4

# Switch to root user to perform package installation
USER root

# Install gettext for envsubst
RUN apk update && apk add --no-cache gettext

# Copy the template file into the container
COPY src/utils/db/config/servers.template.json /tmp/servers.template.json

# Command to run on container start: perform envsubst before entrypoint.sh
ENTRYPOINT [ "sh", "-c", "envsubst < /tmp/servers.template.json > /pgadmin4/servers.json && /entrypoint.sh" ]

```

- Update the `docker-compose.yml` file:
```yml
services:

  postgres:
    container_name: database
    image: postgres:latest
    ports:
      - "${DB_PORT}:${DB_PORT}"
    environment:
      POSTGRES_USER: ${POSTGRES_USER}
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
      POSTGRES_DB: ${DB_NAME}
    volumes:
      - ./data/database:/var/lib/postgresql/data
    restart: unless-stopped

  pgadmin:
    container_name: pgadmin
    build:
      context: .
      dockerfile: Dockerfile.pgadmin
    depends_on:
      - postgres
    ports:
      - "${PG_PORT}:80"
    environment:
      - PGADMIN_DEFAULT_EMAIL=${PGADMIN_DEFAULT_EMAIL}
      - PGADMIN_DEFAULT_PASSWORD=${PGADMIN_DEFAULT_PASSWORD}
      - DB_NAME=${DB_NAME}
      - DB_HOST=${DB_HOST}
      - DB_PORT=${DB_PORT}
      - DB_MAINTENANCE=${DB_MAINTENANCE}
      - POSTGRES_USER=${POSTGRES_USER}
      - POSTGRES_PASSWORD=${POSTGRES_PASSWORD}
    volumes:
      - ./src/utils/db/config/servers.template.json:/tmp/servers.template.json
      - ./data/pgadmin:/var/lib/pgadmin
    restart: unless-stopped

```

It should work now.

### EXTRA: Create a docker-reset script
This file stops and removes all Docker stuff `docker-reset.sh`
```sh
docker compose down &&
docker rmi -f $(docker images -a -q) &&
docker volume prune -f
```

Run the file with `sh docker-reset.sh`