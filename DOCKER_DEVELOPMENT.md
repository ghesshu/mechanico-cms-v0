# Docker Development Setup

This document explains how to run the Strapi monorepo in a Docker development environment with support for external databases.

## Prerequisites

- Docker
- Docker Compose
- External database (PostgreSQL, MySQL, or SQLite)

## Quick Start

### Using External Database (Recommended)

1. **Create environment file:**
   ```bash
   cp .env.example .env
   ```

2. **Configure your database in `.env`:**
   ```bash
   # Example for external PostgreSQL
   DATABASE_CLIENT=postgres
   DATABASE_HOST=your-database-host.com
   DATABASE_PORT=5432
   DATABASE_NAME=strapi_db
   DATABASE_USERNAME=strapi_user
   DATABASE_PASSWORD=your_secure_password
   ```

3. **Start Strapi only:**
   ```bash
   docker-compose -f docker-compose.development.yml up --build strapi
   ```

### Using Local Databases (Development)

1. **Start with local databases:**
   ```bash
   docker-compose -f docker-compose.development.yml --profile local-db up --build
   ```

2. **Access the applications:**
   - Strapi Admin: http://localhost:1337/admin
   - Strapi API: http://localhost:1337/api
   - Adminer (Database UI): http://localhost:8080

3. **Stop the environment:**
   ```bash
   docker-compose -f docker-compose.development.yml down
   ```

## Services Included

### Strapi Application
- **Port:** 1337
- **Environment:** Development with hot reloading
- **Database:** SQLite (default) with PostgreSQL and MySQL available
- **Volumes:** Source code mounted for live editing

### PostgreSQL Database
- **Port:** 5432
- **Database:** strapi
- **Username:** strapi
- **Password:** strapi

### MySQL Database
- **Port:** 3306
- **Database:** strapi
- **Username:** strapi
- **Password:** strapi

### Adminer (Database Management)
- **Port:** 8080
- **Access:** Web-based database administration tool

## Configuration

### Environment Variables

All configuration is now handled through environment variables. Create a `.env` file from the example:

```bash
cp .env.example .env
```

Key environment variables:

#### Database Configuration
```bash
DATABASE_CLIENT=postgres          # postgres, mysql, or sqlite
DATABASE_HOST=localhost           # Database host
DATABASE_PORT=5432               # Database port
DATABASE_NAME=strapi             # Database name
DATABASE_USERNAME=strapi         # Database username
DATABASE_PASSWORD=strapi         # Database password
DATABASE_SSL=false               # Enable SSL connection
```

#### Strapi Configuration
```bash
STRAPI_PORT=1337                 # Port to expose Strapi
NODE_ENV=development             # Environment mode
JWT_SECRET=your-jwt-secret       # JWT secret key
ADMIN_JWT_SECRET=your-admin-jwt  # Admin JWT secret
APP_KEYS=key1,key2,key3,key4     # Application keys
```

### Database Examples

#### External PostgreSQL (AWS RDS, Google Cloud SQL, etc.)
```bash
DATABASE_CLIENT=postgres
DATABASE_HOST=your-rds-instance.region.rds.amazonaws.com
DATABASE_PORT=5432
DATABASE_NAME=strapi
DATABASE_USERNAME=postgres
DATABASE_PASSWORD=your_secure_password
DATABASE_SSL=true
```

#### External MySQL (PlanetScale, etc.)
```bash
DATABASE_CLIENT=mysql
DATABASE_HOST=your-host.psdb.cloud
DATABASE_PORT=3306
DATABASE_NAME=your-database
DATABASE_USERNAME=your-username
DATABASE_PASSWORD=your-password
DATABASE_SSL=true
```

#### SQLite (Local Development)
```bash
DATABASE_CLIENT=sqlite
DATABASE_FILENAME=./data/data.db
```

## Development Workflow

### Hot Reloading

The development setup includes hot reloading for:
- Source code changes in `/packages`
- Configuration changes in `/examples/getstarted/config`
- API and content type changes in `/examples/getstarted/src`

### Accessing Logs

```bash
# View all logs
docker-compose -f docker-compose.development.yml logs

# View Strapi logs only
docker-compose -f docker-compose.development.yml logs strapi

# Follow logs in real-time
docker-compose -f docker-compose.development.yml logs -f strapi
```

### Rebuilding

If you make changes to dependencies or the Dockerfile:

```bash
docker-compose -f docker-compose.development.yml up --build
```

### Accessing the Container

```bash
docker-compose -f docker-compose.development.yml exec strapi sh
```

## Data Persistence

- **Strapi data:** Stored in `strapi_data` volume
- **PostgreSQL data:** Stored in `postgres_data` volume  
- **MySQL data:** Stored in `mysql_data` volume

To reset all data:

```bash
docker-compose -f docker-compose.development.yml down -v
```

## Troubleshooting

### Port Conflicts

If you have local services running on the same ports:

1. Stop local services, or
2. Change ports in `docker-compose.development.yml`

### Permission Issues

If you encounter permission issues with volumes:

```bash
# Fix ownership (Linux/macOS)
sudo chown -R $USER:$USER ./examples/getstarted/data
```

### Build Issues

If the build fails:

1. Clear Docker cache:
   ```bash
   docker system prune -a
   ```

2. Rebuild without cache:
   ```bash
   docker-compose -f docker-compose.development.yml build --no-cache
   ```

## Production Considerations

This setup is optimized for development. For production:

1. Use multi-stage builds
2. Run as non-root user
3. Use production-grade databases
4. Implement proper secrets management
5. Add health checks and monitoring