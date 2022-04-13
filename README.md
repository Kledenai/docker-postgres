# PostgreSql

A postgres database service to run on docker

## running the application without docker compose

We will be using `docker run` to be able to run **getting-started**, before running the service we will need create the volume and network to be used for postgres, this is only in the case for you use the `docker run` in the `docker compose` you won't need create a volume and network before run a service, follow the command below to create a volume:

```bash
docker volume create postgres
```

After running the volume creation command, run the command below to view the created volume:

```bash
docker volume ls
```

This is the expected return:

```bash
DRIVER    VOLUME NAME
local     postgres
```

Having successfully created the local volume, follow the command bellow to create a network:

```bash
docker network create postgres --attachable --driver bridge
```

Note that I am using two options in creating the network, below are the options and what each of them do:

- **attachable**: Enable manual container attachment
- **driver**: Driver to manage the Network

After running the network creation command, run the command below to view the created network:

```bash
docker network ls
```

This is the expected return:

```bash
NETWORK ID     NAME       DRIVER    SCOPE
4eba0f206c3a   postgres   bridge    local
```

Everything working out we can go to the next step, follow the line below to able to run the postgres service:

```bash
docker run -dp 5432:5432 -e POSTGRES_PRIMARY_USER="test" -e POSTGRES_PRIMARY_PASSWORD="CHANGEME" -e POSTGRES_ROOT_PASSWORD="CHANGEME" -e POSTGRES_DATABASE="example" -e POSTGRES_USER="kledenai" -e POSTGRES_PASSWORD="CHANGEME" --volume postgres:/var/lib/postgresql/data --network postgres postgres
```

Everything working out we can go to the next step, follow the line below to able to run the pgadmin service:

```bash
docker run -dp 80:80 -e PGADMIN_DEFAULT_EMAIL="test@example.com" -e PGADMIN_DEFAULT_PASSWORD="CHANGEME" --network postgres dpage/pgadmin4
```

Note that we are using the same network as **postgres**, as we need to make the containers have this entry so that **pgadmin** can access **postgres**.

After the services running you can run the command below to check the health of the containers:

```bash
docker ps
```

This is the expected return:

```bash
CONTAINER ID   IMAGE            COMMAND                  CREATED          STATUS          PORTS                                        NAMES
dc750a9d1e39   dpage/pgadmin4   "/entrypoint.sh"         22 seconds ago   Up 16 seconds   0.0.0.0:80->80/tcp, :::80->80/tcp, 443/tcp   hardcore_shtern
647bf9d5186e   postgres         "docker-entrypoint.s…"   3 minutes ago    Up 3 minutes    0.0.0.0:5432->5432/tcp, :::5432->5432/tcp    peaceful_tu
```

Having had this return with this result, let's access the service, in your browser access `localhost:80` and you will see the **pgadmin** login screen.

## running the application with docker compose

We will use docker-compose to be able to run the script already prepared to be able to run the service in docker, follow the example below of what is expected to be in the file:

```bash
version: "3.9"
services:
  postgres:
    image: postgres
    environment:
      POSTGRES_PRIMARY_USER: "${POSTGRES_PRIMARY_USER}"
      POSTGRES_PRIMARY_PASSWORD: "${POSTGRES_PRIMARY_PASSWORD}"
      POSTGRES_ROOT_PASSWORD: "${POSTGRES_ROOT_PASSWORD}"
      POSTGRES_DATABASE: "${POSTGRES_DATABASE}"
      POSTGRES_USER: "${POSTGRES_USER}"
      POSTGRES_PASSWORD: "${POSTGRES_PASSWORD}"
    ports:
      - "${POSTGRES_PORT}:5432"
    volumes:
      - postgres:/var/lib/postgresql/data
    networks:
      - postgres
  pgadmin:
    image: dpage/pgadmin4
    environment:
      PGADMIN_DEFAULT_EMAIL: "${PGADMIN_EMAIL}"
      PGADMIN_DEFAULT_PASSWORD: "${PGADMIN_PASSWORD}"
    ports:
      - "${PGADMIN_PORT}:80"
    depends_on:
      - postgres
    networks:
      - postgres

volumes:
  postgres:

networks:
  postgres:
    driver: bridge
```

Note that variable references are being passed, which are being taken from a .env file, you will need to have an .env file in your project root containing the same parameters below:

```bash
POSTGRES_PORT=
POSTGRES_PRIMARY_USER=
POSTGRES_PRIMARY_PASSWORD=
POSTGRES_USER=
POSTGRES_PASSWORD=
POSTGRES_ROOT_PASSWORD=
POSTGRES_DATABASE=

PGADMIN_EMAIL=
PGADMIN_PASSWORD=
PGADMIN_PORT=
```

Add the values ​​and then save the file, having done that we can run the file.

Only this is enough for us to run the service, if everything is ok, run the command below:

```bash
docker-compose up -d
```

After run the command you can run the command below to check the health of the containers:

```bash
docker ps
```

This is the expected return:

```bash
CONTAINER ID   IMAGE            COMMAND                  CREATED          STATUS          PORTS                                        NAMES
dc750a9d1e39   dpage/pgadmin4   "/entrypoint.sh"         22 seconds ago   Up 16 seconds   0.0.0.0:80->80/tcp, :::80->80/tcp, 443/tcp   hardcore_shtern
647bf9d5186e   postgres         "docker-entrypoint.s…"   3 minutes ago    Up 3 minutes    0.0.0.0:5432->5432/tcp, :::5432->5432/tcp    peaceful_tu
```

Having had this return with this result, let's access the service, in your browser access `localhost:80` and you will see the **pgadmin** login screen.

The service name has been set to `postgres`, this will be the **hostname** used when you try to access the server via **pgadmin**.

Everything being in agreement you have already managed to run the postgres in docker.
