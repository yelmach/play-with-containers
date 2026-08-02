# Play With Containers

## Overview
This project demonstrates a fully containerized microservices architecture using Docker and Docker Compose. It features isolated services communicating through dedicated Docker networks, persistent data management via Docker volumes, and an asynchronous message queue.

## Architecture
- **API Gateway**: Entry point for all external requests (Port 3000).
- **Inventory Service**: Manages movie inventory, backed by a PostgreSQL database.
- **Billing Service**: Processes billing events asynchronously via RabbitMQ, backed by a PostgreSQL database.
- **RabbitMQ**: Message broker facilitating communication between the API Gateway and Billing service.

## Prerequisites
- **OS**: Linux environment (Ubuntu recommended).
- **Docker**: Docker Engine installed and running.
- **Docker Compose**: Docker Compose plugin installed.

## Configuration
Before starting the infrastructure, you must configure your environment variables. 
Do **not** commit your actual `.env` file to version control.

1. Copy the example configuration file:
   ```bash
   cp .env.example .env
   ```
2. Update the `.env` file with your secure database credentials, RabbitMQ passwords, and preferred host configurations.

## Setup & Deployment
The entire infrastructure is targeted and managed via Docker Compose.

To build the optimized Alpine images and start the containers in detached mode:
```bash
docker-compose up --build -d
```

To verify all containers are running and healthy:
```bash
docker ps
```

## Usage
The API Gateway exposes the infrastructure on `http://localhost:3000`.

### Inventory API
**Add a new movie (POST):**
```bash
curl -X POST http://localhost:3000/api/movies/ \
     -H "Content-Type: application/json" \
     -d '{"title": "A new movie", "description": "Very short description"}'
```

**List movies (GET):**
```bash
curl http://localhost:3000/api/movies/
```

### Billing API
**Submit a billing event (POST):**
```bash
curl -X POST http://localhost:3000/api/billing/ \
     -H "Content-Type: application/json" \
     -d '{"user_id": "20", "number_of_items": "99", "total_amount": "250"}'
```

## Infrastructure Management & Teardown

To monitor the logs of a specific service in real-time:
```bash
docker logs -f billing-app
```

To restart a specific service to test queue persistence:
```bash
docker restart billing-app
```

to check orders table:
```bash
docker exec -it billing-db psql -U billing_user -d billing_db -c "SELECT * FROM orders;"
```

To stop the infrastructure without deleting your database volumes:
```bash
docker-compose stop
```

To completely tear down the infrastructure, remove the containers, networks, and destroy all data volumes:
```bash
docker-compose down -v
```