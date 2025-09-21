# MariaDB and phpMyAdmin Docker Compose Documentation

## Overview

This Docker Compose configuration sets up a MariaDB database server with phpMyAdmin, a web-based database management interface. MariaDB is a popular open-source relational database that is fully compatible with MySQL. This setup is ideal for development environments where you need a relational database with an administrative GUI.

## Prerequisites

- Docker installed on your system
- Docker Compose (usually included with Docker Desktop)

## Services

### 1. MariaDB Database (db)

**Image**: [`mariadb:10.6`](https://hub.docker.com/_/mariadb)
**Platform**: linux/amd64 (default)

#### Configuration

- **Volume**: Persistent storage at `/var/lib/mysql` using a named volume `mariadb_data`
- **Restart Policy**: Always restart if container stops
- **Port Mapping**: Host port 3306 to container port 3306
- **Environment Variables**:
  - `MYSQL_ROOT_PASSWORD`: Root password (default: 'rootpassword')
  - `MYSQL_DATABASE`: Initial database to create (default: 'mydatabase')
  - `MYSQL_USER`: Initial user to create (default: 'myuser')
  - `MYSQL_PASSWORD`: Password for the initial user (default: 'userpassword')

#### Usage

To connect to the MariaDB server directly:

```bash
mysql -h 127.0.0.1 -P 3306 -u root -p
```

### 2. phpMyAdmin

**Image**: [`phpmyadmin:latest`](https://hub.docker.com/r/phpmyadmin/phpmyadmin)
**Platform**: linux/amd64 (default)

#### Configuration

- **Dependency**: Requires the `db` service to be running
- **Restart Policy**: Always restart if container stops
- **Port Mapping**: Host port 8090 to container port 80
- **Environment Variables**:
  - `PMA_HOST`: Database hostname (set to 'db' - the MariaDB service name)
  - `MYSQL_ROOT_PASSWORD`: Root password (must match the db service)
  - `UPLOAD_LIMIT`: Maximum file upload size (set to 128MB)

#### Usage

Access phpMyAdmin through your web browser at:

```
http://localhost:8090
```

Login credentials:

- Username: `root`
- Password: The value set for `DB_ROOT_PASSWORD` (default: 'rootpassword')

## Environment Variables

You can customize the configuration using environment variables either by:

1. Setting them in your shell before running Docker Compose
2. Creating a `.env` file in the same directory

Available variables with their defaults:

```bash
DB_ROOT_PASSWORD=rootpassword
DB_NAME=mydatabase
DB_USER=myuser
DB_PASSWORD=userpassword
```

## Volumes

- `mariadb_data`: Persistent volume for MariaDB data storage using the local driver

## Deployment

1. Save the Docker Compose configuration as `docker-compose.yml`
2. (Optional) Create a `.env` file to customize environment variables
3. Run the following command:
   ```bash
   docker-compose up -d
   ```

## Management

- Start services: `docker-compose up -d`
- Stop services: `docker-compose down`
- View logs: `docker-compose logs`
- Monitor containers: `docker-compose ps`

## Security Notes

1. Change the default passwords for production environments
2. Avoid exposing database port (3306) publicly without proper security measures
3. Consider using more secure authentication methods for production
4. MariaDB uses the same protocol and is compatible with MySQL, so phpMyAdmin works seamlessly with it

## References

- MariaDB Docker Hub: https://hub.docker.com/_/mariadb
- phpMyAdmin Docker Hub: https://hub.docker.com/r/phpmyadmin/phpmyadmin
- Docker Documentation: https://docs.docker.com/
- Docker Compose Documentation: https://docs.docker.com/compose/

## Troubleshooting

1. If you encounter connection issues, verify that both containers are running: `docker-compose ps`
2. Check logs for specific errors: `docker-compose logs db` or `docker-compose logs phpmyadmin`
3. Ensure port 3306 is not already in use on your host system
4. Verify that the volume permissions allow the MariaDB container to write data

## Differences from MySQL Version

- Uses MariaDB 10.6 instead of MySQL 8.0
- Volume name changed from `mysql_data` to `mariadb_data`
- Platform specification removed as MariaDB image is multi-architecture by default
- MariaDB is a community-developed fork of MySQL with some enhanced features and performance improvements

