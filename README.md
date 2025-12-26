# BookStack with Docker Compose

A ready-to-use Docker Compose configuration to deploy [BookStack](https://www.bookstackapp.com/) with MySQL database.

## 📋 Description

BookStack is an open-source platform for creating documentation and wikis. This configuration includes:
- **BookStack** (latest version via LinuxServer.io)
- **MySQL 8.0** as database
- Isolated network for inter-service communication
- Persistent volumes for data and configurations
- Health check to ensure database availability

## 🔧 Prerequisites

- [Docker](https://docs.docker.com/get-docker/) installed
- [Docker Compose](https://docs.docker.com/compose/install/) installed
- Available ports: `6875` (BookStack)

## 📁 Project Structure

```
.
├── docker-compose.yml    # Container configuration
├── .env                  # Environment variables (don't commit!)
├── .env.example          # Environment variables template
└── README.md            # This file
```

## 🚀 Installation and Startup

### 1. Clone the repository

```bash
git clone <repository-url>
cd BookStack
```

### 2. Configure environment variables

Copy the `.env.example` file to `.env` and customize the credentials:

```bash
cp .env.example .env
```

Edit the `.env` file with your credentials:

```env
# MySQL Configuration
MYSQL_ROOT_PASSWORD=your_secure_root_password
MYSQL_DATABASE=bookstackapp
MYSQL_USER=bookstack
MYSQL_PASSWORD=your_secure_password

# BookStack Configuration
APP_URL=http://localhost:6875
APP_KEY=base64:mgeRV0KIdEajBJY2d8urgyyrwRMN9h53xZOozYHw+dM=
```

> ⚠️ **Important**: Don't commit the `.env` file to Git! It's already included in `.gitignore`.

### 3. Generate a new APP_KEY (optional but recommended)

To generate a new application key:

```bash
docker run -it --rm --entrypoint /bin/bash lscr.io/linuxserver/bookstack:latest appkey
```

Copy the output (e.g., `base64:...`) and update it in the `.env` file.

### 4. Start the containers

```bash
docker-compose up -d
```

The containers will start in the background. The database will take a few seconds to become "healthy" before BookStack starts.

### 5. Verify the status

```bash
docker-compose ps
```

You should see both containers in "Up" or "Healthy" status.

## 🌐 Accessing BookStack

Open your browser and go to: **http://localhost:6875**

### Default credentials

- **Email**: `admin@admin.com`
- **Password**: `password`

> ⚠️ **Change the credentials immediately** after first login!

## 🛠️ Useful Commands

### View logs

```bash
# All services
docker-compose logs -f

# BookStack only
docker-compose logs -f bookstack

# MySQL only
docker-compose logs -f db
```

### Stop containers

```bash
docker-compose stop
```

### Restart containers

```bash
docker-compose restart
```

### Stop and remove containers (keeps volumes)

```bash
docker-compose down
```

### Remove everything including volumes (⚠️ you'll lose all data!)

```bash
docker-compose down -v
```

### Update images

```bash
docker-compose pull
docker-compose up -d
```

## 📊 Volumes and Persistence

Data is saved in named Docker volumes:

- `db_data`: MySQL database data
- `bookstack_config`: BookStack configurations and uploads

To make backups:

```bash
# Database backup
docker exec bookstack_db mysqldump -u bookstack -p<password> bookstackapp > backup.sql

# Volume backup
docker run --rm -v bookstack_bookstack_config:/data -v $(pwd):/backup alpine tar czf /backup/bookstack-config-backup.tar.gz /data
```

## 🔍 Troubleshooting

### BookStack won't start - "Application key is missing"

Make sure you have configured `APP_KEY` in the `.env` file. Generate a new key with:

```bash
docker run -it --rm --entrypoint /bin/bash lscr.io/linuxserver/bookstack:latest appkey
```

### Database connection error

1. Verify that MySQL is healthy:
   ```bash
   docker-compose ps
   ```

2. Check the database logs:
   ```bash
   docker-compose logs db
   ```

3. Verify that the credentials in the `.env` file are correct

### Complete reset (deletes all data)

```bash
docker-compose down -v
docker-compose up -d
```

### Port 6875 already in use

Modify the port in `docker-compose.yml`:

```yaml
ports:
  - "8080:80"  # Change 6875 to your preferred port
```

Also update `APP_URL` in the `.env` file.

## 🔐 Security

- **Don't expose MySQL** to the Internet (port 3306 is commented out in docker-compose.yml)
- **Use strong passwords** for MySQL and BookStack admin user
- **Don't commit** the `.env` file to the repository
- **Regularly update** Docker images
- **Configure a reverse proxy** (nginx/traefik) with HTTPS in production

## 📚 Resources

- [BookStack Documentation](https://www.bookstackapp.com/docs/)
- [BookStack on GitHub](https://github.com/BookStackApp/BookStack)
- [LinuxServer Docker Image](https://docs.linuxserver.io/images/docker-bookstack)

## 📄 License

This configuration project is released as public domain. BookStack is released under the MIT license.
