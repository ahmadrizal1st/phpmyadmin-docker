# MySQL/MariaDB with phpMyAdmin Docker Compose Documentation

## Overview

This Docker Compose configuration provides a complete database solution with either MySQL or MariaDB as the database server and phpMyAdmin as the web-based management interface. This setup is ideal for development environments, testing, and learning purposes.

## Prerequisites

- Docker installed on your system
- Docker Compose (usually included with Docker Desktop)

## Configuration Options

### Option 1: MySQL Version (Recommended for MySQL-specific features)

```yaml
services:
  db:
    image: mysql:8.0
    platform: linux/amd64
    volumes:
      - mysql_data:/var/lib/mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: ${DB_ROOT_PASSWORD:-rootpassword}
      MYSQL_DATABASE: ${DB_NAME:-mydatabase}
      MYSQL_USER: ${DB_USER:-myuser}
      MYSQL_PASSWORD: ${DB_PASSWORD:-userpassword}
    ports:
      - "3306:3306"

  phpmyadmin:
    depends_on:
      - db
    image: phpmyadmin:latest
    platform: linux/amd64
    restart: always
    ports:
      - "8090:80"
    environment:
      PMA_HOST: db
      MYSQL_ROOT_PASSWORD: ${DB_ROOT_PASSWORD:-rootpassword}
      UPLOAD_LIMIT: 128M

volumes:
  mysql_data:
    driver: local
```

### Option 2: MariaDB Version (Recommended for open-source alternative)

```yaml
services:
  db:
    image: mariadb:10.6
    volumes:
      - mariadb_data:/var/lib/mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: ${DB_ROOT_PASSWORD:-rootpassword}
      MYSQL_DATABASE: ${DB_NAME:-mydatabase}
      MYSQL_USER: ${DB_USER:-myuser}
      MYSQL_PASSWORD: ${DB_PASSWORD:-userpassword}
    ports:
      - "3306:3306"

  phpmyadmin:
    depends_on:
      - db
    image: phpmyadmin:latest
    restart: always
    ports:
      - "8090:80"
    environment:
      PMA_HOST: db
      MYSQL_ROOT_PASSWORD: ${DB_ROOT_PASSWORD:-rootpassword}
      UPLOAD_LIMIT: 128M

volumes:
  mariadb_data:
    driver: local
```

## Services

### 1. Database Service (db)

**Available Images**:

- MySQL: [`mysql:8.0`](https://hub.docker.com/_/mysql)
- MariaDB: [`mariadb:10.6`](https://hub.docker.com/_/mariadb)

#### Configuration

- **Volume**: Persistent storage at `/var/lib/mysql` using a named volume
- **Restart Policy**: Always restart if container stops
- **Port Mapping**: Host port 3306 to container port 3306
- **Environment Variables**:
  - `MYSQL_ROOT_PASSWORD`: Root password (default: 'rootpassword')
  - `MYSQL_DATABASE`: Initial database to create (default: 'mydatabase')
  - `MYSQL_USER`: Initial user to create (default: 'myuser')
  - `MYSQL_PASSWORD`: Password for the initial user (default: 'userpassword')

#### Direct Database Access

Connect to the database server directly:

```bash
mysql -h 127.0.0.1 -P 3306 -u root -p
```

### 2. phpMyAdmin Service

**Image**: [`phpmyadmin:latest`](https://hub.docker.com/r/phpmyadmin/phpmyadmin)

#### Configuration

- **Dependency**: Requires the `db` service to be running
- **Restart Policy**: Always restart if container stops
- **Port Mapping**: Host port 8090 to container port 80
- **Environment Variables**:
  - `PMA_HOST`: Database hostname (set to 'db' - the database service name)
  - `MYSQL_ROOT_PASSWORD`: Root password (must match the db service)
  - `UPLOAD_LIMIT`: Maximum file upload size (set to 128MB)

#### Web Interface Access

Access phpMyAdmin through your web browser at:

```
http://localhost:8090
```

Login credentials:

- Username: `root`
- Password: The value set for `DB_ROOT_PASSWORD` (default: 'rootpassword')

## Environment Variables

Customize the configuration using environment variables either by:

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

- **MySQL version**: `mysql_data` - Persistent volume for MySQL data storage
- **MariaDB version**: `mariadb_data` - Persistent volume for MariaDB data storage
- Both use the local driver for data persistence

## Deployment

1. Choose either MySQL or MariaDB version and save as `docker-compose.yml`
2. (Optional) Create a `.env` file to customize environment variables
3. Run the following command:
   ```bash
   docker-compose up -d
   ```

## Management Commands

- Start services: `docker-compose up -d`
- Stop services: `docker-compose down`
- View logs: `docker-compose logs`
- Monitor containers: `docker-compose ps`
- Stop services but keep volumes: `docker-compose down --volumes` (to remove volumes, omit the flag)

## Security Considerations

1. **Always change default passwords** for production environments
2. Avoid exposing database port (3306) publicly without proper security measures
3. Consider using Docker networks for internal communication only
4. Use strong, complex passwords for database users
5. Regularly update Docker images to receive security patches

## MySQL vs MariaDB: Key Differences

| Aspect | MySQL | MariaDB |
|--------|-------|---------|
| License | Oracle-owned (GPLv2) | Open-source (GPLv2) |
| Performance | Good for enterprise workloads | Often faster for read operations |
| Features | Oracle-specific enhancements | Community-driven enhancements |
| Compatibility | Standard MySQL | MySQL compatible with extras |
| Storage Engines | Standard set | Additional engines like Aria, ColumnStore |

## References

- MySQL Docker Hub: https://hub.docker.com/_/mysql
- MariaDB Docker Hub: https://hub.docker.com/_/mariadb
- phpMyAdmin Docker Hub: https://hub.docker.com/r/phpmyadmin/phpmyadmin
- Docker Documentation: https://docs.docker.com/
- Docker Compose Documentation: https://docs.docker.com/compose/

## Troubleshooting

### Common Issues

1. **Connection refused errors**: Verify both containers are running with `docker-compose ps`
2. **Port conflicts**: Ensure ports 3306 and 8090 are not already in use
3. **Permission errors**: Check volume permissions for database data directory
4. **Platform issues**: For ARM devices, you may need to adjust platform settings

### Log Inspection

Check service logs for detailed error information:

```bash
docker-compose logs db        # Database logs
docker-compose logs phpmyadmin # phpMyAdmin logs
docker-compose logs -f        # Follow all logs in real-time
```

### Data Persistence

- Data persists between container restarts due to named volumes
- To completely reset, remove volumes with `docker-compose down -v`
- Volume data is stored in Docker's managed volume directory

## Support

For additional support:

- Check official Docker documentation for both MySQL and MariaDB
- Review phpMyAdmin documentation for web interface issues
- Consult Docker community forums for platform-specific issues

