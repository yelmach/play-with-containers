#### General

##### Check the Repo content

A `README.md` file and all files used to create, delete and manage the learner infrastructure must be submitted in the repo.

###### Are all the required files present?

###### Was the `.env` file excluded from the git files?

###### Are all pushed files in the repo clean of any credentials or passwords?

##### Ask the following questions to the group or learner:

- What are containers and what are their advantages?

- What is the difference between containers and virtual machines?

- What is Docker and what is it used for?

###### Did the learner reply correctly to all the above questions?

##### Open and read the `README.md` file provided by the learner.

###### Does the `README.md` file contain all the required information to run and manage the solution (prerequisites, configuration, setup, usage, etc)?

##### Check the learner infrastructure.

###### Does the learner architecture reflect the infrastructure enforced by the subject?

##### Run the learner infrastructure:

```console
user:~$ docker-compose up
<...>
inventory-db         ... done
billing-db           ... done
inventory-app        ... done
billing-app          ... done
rabbit-queue         ... done
api-gateway-app      ... done
<...>
user:~$
```

###### Does the infrastructure start correctly?

##### Ask the following questions to the group or learner:

- What is a microservices architecture?

- Why do we use microservices architecture?

- What is a queue and what is it used for?

- What is RabbitMQ?

###### Did the learner reply correctly to all the above questions?

#### Verify the Dockerfiles:

###### Is there a Dockerfile for each service?

###### Are all Dockerfiles based on `Debian` or `Alpine`?

###### Are Dockerfiles or any other solution files free from sensitive data (sensitive data should only exist in `.env` file)?

##### Ask the following questions to the group or learner:

- What is a Dockerfile?

- Explain the instructions used on the Dockerfile.

###### Did the learner reply correctly to all the above questions?

#### Check the Containers:

```console
user:~$ docker ps
CONTAINER ID   IMAGE            COMMAND CREATED STATUS         PORTS                                 NAMES
<...>       inventory-db            <...> <...> <...>          5432/tcp                              inventory-db
<...>       billing-db              <...> <...> <...>          5432/tcp                              billing-db
<...>       inventory-app           <...> <...> <...>          8080/tcp                              inventory-app
<...>       billing-app             <...> <...> <...>          8080/tcp                              billing-app
<...>       rabbit-queue            <...> <...> <...>          5672/tcp                              rabbit-queue
<...>       api-gateway-app         <...> <...> <...>         0.0.0.0:3000->3000/tcp, :::3000->3000/tcp  api-gateway-app
user:~$
```

- The `inventory-db` container is a PostgreSQL database server that contains the
  inventory database. It must be accessible via port `5432`.
- The `billing-db` container is a PostgreSQL database server that contains the billing database. It must be accessible via port `5432`.
- The `inventory-app` container is a server that contains the
  inventory-app. It will be connected to the inventory database and accessible
  via port `8080`.
- The `billing-app` container is a server that contains the billing-app.
  It will be connected to the billing database and will consume messages from
  the RabbitMQ queue. It will be accessible via port `8080`.
- The `rabbit-queue` container is a RabbitMQ server that contains the queue.
- The `api-gateway-app` container is a server that contains the
  API gateway. It will forward the requests to the other services, and it is
  accessible via port `3000`.

##### Check the Container restart policy:

```console
user:~$ docker inspect -f "{{ .HostConfig.RestartPolicy }}" <container-name>
{on-failure 0}
user:~$
```

###### Do all containers have the correct configuration?

###### Are the containers configured to restart in case of failure?

#### Check the Docker volumes:

```console
user:~$ docker volume ls
DRIVER    VOLUME NAME
<...>     inventory-db
<...>     billing-db
<...>     api-gateway-app
user:~$
```

- `inventory-db` volume contains your inventory database.
- `billing-db` volume contains your billing database.
- `api-gateway-app` volume contains your API gateway logs.

###### Do all volumes have the correct configuration?

##### Ask the following questions to the group or learner:

- What is a Docker volume?

- Why do we use Docker volumes?

###### Did the learner reply correctly to all the above questions?

#### Check the solution network:

###### Is the connection to the api-gateway-app the only one exposed from outside of the Docker host?

##### Ask the following questions to the group or learner:

- What is the Docker network?

- Why do we use the Docker network?

###### Did the learner reply correctly to all the above questions?

#### Check the Docker images:

```console
user:~$ docker images
REPOSITORY              TAG          IMAGE ID       CREATED        SIZE
inventory-db           <...>          <...>         <...>          <...>
billing-db             <...>          <...>         <...>          <...>
inventory-app          <...>          <...>         <...>          <...>
billing-app            <...>          <...>         <...>          <...>
rabbit-queue           <...>          <...>         <...>          <...>
api-gateway-app        <...>          <...>         <...>          <...>
user:~$
```

###### Is there a Docker image for each service with the same service name?

##### Ask the following questions to the group or learner:

- What is a Docker image?

- Why do we use Docker images?

###### Did the learner reply correctly to all the above questions?

#### Inventory API Endpoints

##### Open Postman and make a `POST` request to `http://[GATEWAY_IP]:[GATEWAY_PORT]/api/movies/` address with the following body as `Content-Type: application/json`:

```json
{
  "title": "A new movie",
  "description": "Very short description"
}
```

###### Can you confirm the response was the success code `201`?

##### In Postman make a `GET` request to `http://[GATEWAY_IP]:[GATEWAY_PORT]/api/movies/` address.

###### Can you confirm the response was success code `200` and the body of the response is in `json` with the information of the last added movie?

#### Billing API Endpoints

##### Open Postman and make a `POST` request to `http://[GATEWAY_IP]:[GATEWAY_PORT]/api/billing/` address with the following body as `Content-Type: application/json`:

```json
{
  "user_id": "20",
  "number_of_items": "99",
  "total_amount": "250"
}
```

###### Can you confirm the response was success code `200`?

##### Restart the billing-app container.

###### After restarting the billing-app container, are the queued billing messages processed successfully?

###### Does the billing-app automatically reconnect to RabbitMQ after restart?

##### Stop the billing-app container.

###### Can you confirm the `billing-app` container was correctly stopped?

##### Open Postman and make a `POST` request to `http://[GATEWAY_IP]:[GATEWAY_PORT]/api/billing/` address with the following body as `Content-Type: application/json`:

```json
{
  "user_id": "22",
  "number_of_items": "10",
  "total_amount": "50"
}
```

###### Can you confirm the response was success code `200` even if the `billing_app` is not working?

#### Database Verification

###### Can you connect to the inventory-db container and confirm the inventory database and tables exist?

###### Can you connect to the billing-db container and confirm the billing database and tables exist?

###### Are the databases persisting data after container restart?

#### Dockerfile Optimization

###### Are Dockerfile layers optimized to avoid unnecessary or duplicated layers?

###### Is the final image size minimized by proper instruction ordering and cleanup?

#### Bonus

###### +Did the learner use his/her own `crud-master` solution?

###### +Did the learner add any optional bonus?

###### +Is this project an outstanding project?