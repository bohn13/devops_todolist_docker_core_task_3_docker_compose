# TodoApp with MySQL (Docker Compose)

This project contains a Django application and a MySQL database, orchestrated using Docker Compose.

---

## Prerequisites
- [Docker](https://docs.docker.com/get-docker/)
- [Docker Compose](https://docs.docker.com/compose/install/)
---

## 1. Quick Start
To build and start all services (Database + Application) in the background, run:

```bash
docker-compose up -d --build
```

The application will be accessible at: [http://127.0.0.1:8080/](http://127.0.0.1:8080/)

---

## 2. Managing Containers

### Stop Containers

To stop the running services without removing them:

```bash
docker-compose stop
```

### Stop and Remove Containers

To stop and remove containers, networks, and images created by `up`:

```bash
docker-compose down
```

### View Logs

If you need to debug or check the application status:

```bash
# All logs
docker-compose logs -f

# Specific service logs
docker-compose logs -f web
```

---

## 3. Database Persistence

The MySQL data is stored using a **Docker Volume**. This ensures that your todos are not lost even if the containers are removed.

* **Volume Name:** `db-data`
* **Mount Point:** `/var/lib/mysql`

To completely reset the database (including all data), run:

```bash
docker-compose down -v
```

---

## 4. Technical Details

### Automated Migrations

The application is configured to automatically handle database setup. The `ENTRYPOINT` in the Dockerfile performs the following actions upon container start:

1. Runs `python manage.py migrate` to prepare the MySQL schema.
2. Starts the Django development server on `0.0.0.0:8000`.

### Service Discovery

The application connects to the database using the service name `db` (defined in `docker-compose.yml`) via the `DB_HOST` environment variable.

### Port Mapping

* **Web App:** Maps internal port `8000` to host port `8080`.
* **Database:** Maps internal port `3306` to host port `3306` for local debugging access.
---

## 5. Development

If you make changes to the `settings.py` or the `Dockerfile`, rebuild the images to apply changes:

```bash
docker-compose up -d --build
```
