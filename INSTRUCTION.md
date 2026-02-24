
# TodoApp with MySQL (Docker Compose)

This project contains a Django application and a MySQL database, orchestrated using Docker Compose to ensure a reliable and reproducible environment.

---

## Prerequisites
- [Docker](https://docs.docker.com/get-docker/)
- [Docker Compose](https://docs.docker.com/compose/install/)

---

## 1. Quick Start
To build and start all services (Database + Application) with a single command, run:

```bash
docker-compose up -d --build
```

The application will be accessible at: [http://127.0.0.1:8080/](http://127.0.0.1:8080/)

---

## 2. Managing Containers

### Stop and Remove Containers

To stop and remove containers and networks:

```bash
docker-compose down
```

### Stop and Remove with Volumes

To reset the database and delete all stored tasks:

```bash
docker-compose down -v
```

### View Logs

To monitor the startup process or debug service issues:

```bash
# View logs for the Django application
docker-compose logs -f web

# View logs for the MySQL database
docker-compose logs -f db
```

---

## 3. Database Persistence

The MySQL data is stored in a persistent **Docker Volume**. This ensures that your tasks are preserved even if the containers are removed or the system is restarted.

* **Volume Name:** `db-data`
* **Internal Path:** `/var/lib/mysql`
---

## 4. Technical Details & Reliability

### DB Readiness Strategy

To prevent startup failures caused by race conditions, the project implements a multi-layer readiness strategy:

1. **MySQL Healthcheck:** The `db` service uses `mysqladmin ping` to verify it is ready to accept connections.
2. **Service Dependencies:** The `web` service is configured with `condition: service_healthy`, ensuring it only starts after the database is fully initialized.
3. **Wait-for Logic:** The application `ENTRYPOINT` includes a brief initialization delay to ensure stable migration execution.

### Automated Migrations

The `ENTRYPOINT` in the `Dockerfile` automatically handles the environment setup upon container start:

1. Waits for the DB service to be reachable.
2. Executes `python manage.py migrate` to apply database schema changes.
3. Starts the Django server on `0.0.0.0:8000`.

### Networking & Service Discovery

* **Service Name:** The app connects to the database using the internal hostname `db`.
* **Port Mapping:** The web application maps internal port `8000` to host port `8080`.
* **MySQL Port:** The database port `3306` is kept internal to avoid conflicts with local MySQL instances on the host machine.
---

## 5. Troubleshooting

If the application fails to start on the first run:

1. Check the logs using `docker-compose logs -f web`.
2. Ensure `mysql-connector-python` (or a compatible driver) is listed in `requirements.txt`.
3. If a migration error occurs due to a timeout, restart the web service:
```bash
docker-compose restart web
```
