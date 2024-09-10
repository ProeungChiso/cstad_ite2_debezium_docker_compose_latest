# 💥My Configure for Deploy Debezium Service.
👉 Link notion: https://rogue-consonant-00a.notion.site/Deploy-Debezium-32efed8d10fb4585a1de87ac7dd14b15?pvs=74

# 🌀Kafka Ecosystem Docker Compose Setup

👉 This setup provides an ecosystem using Docker Compose, including Zookeeper, Kafka Broker, Debezium, Schema Registry, Kafka REST Proxy, Debezium UI, and PostgreSQL. This is particularly useful for streaming data from PostgreSQL to Kafka using Debezium.

# ⭕Services Overview

👉 Zookeeper
  - Image:`confluentinc/cp-zookeeper:7.3.1`
  - Purpose: Coordinates and manages Kafka brokers.
  - Ports: 
    - Exposes port `2181` for client connections.
  - Healthcheck: Check if the Zookeeper service is reachable on port 2181.
  - Network: `kafka-network`.

👉 Kafka Broker
  - Image: `confluentinc/cp-kafka:7.3.1`
  - Purpose: Manages message streaming and replication.
  - Ports: 
    - Exposes port `9092` for broker communication.
    - Exposes port `9101` for JMX monitoring.
  - Environment Variables:
    - Connects to Zookeeper (`KAFKA_ZOOKEEPER_CONNECT`).
    - Listens on all network interfaces (`KAFKA_LISTENERS`).
    - Advertises listener for external access (`KAFKA_ADVERTISED_LISTENERS`).
    - Various configurations for replication, offsets, transactions, and message sizes.
  - Healthcheck: Verifies that the broker is reachable on port 9092.
  - Network: `kafka-network`.

👉 Debezium
  - Image: `debezium/connect:latest`
  - Purpose: Connects to the Kafka broker to capture changes from databases (e.g., PostgreSQL) and publish them to Kafka topics.
  - Ports:
    - Exposes port `8083` for Kafka Connect API.
  - Environment Variables:
    - Defines Kafka broker (`BOOTSTRAP_SERVERS`), storage topics, and JSON converters.
    - Enables scripting support for transformations.
  - Healthcheck: Checks if the Debezium connector is available by accessing `/connectors`.
  - Network: `kafka-network`.

👉 Schema Registry
  - Image: `confluentinc/cp-schema-registry:7.3.1`
  - Purpose: Stores and retrieves schemas for Kafka topics.
  - Ports: 
    - Exposes port `8081` for Schema Registry API.
  - Environment Variables:
    - Connects to Kafka broker (`SCHEMA_REGISTRY_KAFKASTORE_BOOTSTRAP_SERVERS`).
    - Listens on all network interfaces (`SCHEMA_REGISTRY_LISTENERS`).
  - Healthcheck: Verifies availability of Schema Registry by querying `/subjects`.
  - Network: `kafka-network`.

👉 Kafka REST Proxy
  - Image: `confluentinc/cp-kafka-rest:7.3.1`
  - Purpose: Provides a RESTful interface to interact with Kafka topics.
  - Ports: 
    - Exposes port `8082` for the REST API.
  - Environment Variables:
    - Connects to the Kafka broker (`KAFKA_REST_BOOTSTRAP_SERVERS`).
    - Listens on all network interfaces (`KAFKA_REST_LISTENERS`).
  - Network: `kafka-network`.

👉 Debezium UI
  - Image: `debezium/debezium-ui:latest`
  - Purpose: Web UI for managing and monitoring Debezium connectors.
  - Ports: 
    - Exposes port `8080` for the web interface.
  - Environment Variables:
    - Connects to the Debezium Kafka Connect API (`KAFKA_CONNECT_URIS`).
  - Network: `kafka-network`.

👉 PostgreSQL
  - Image: `postgres:latest`
  - Purpose: PostgreSQL database configured to support logical replication for Debezium.
  - Ports: 
    - Exposes port `5432` for database connections.
  - Environment Variables:
    - Default PostgreSQL user, password, and database (`POSTGRES_USER`, `POSTGRES_PASSWORD`, `POSTGRES_DB`).
  - Volumes:
    - Mounts initialization scripts from `./scripts` to `/docker-entrypoint-initdb.d`.
  - Healthcheck: Checks PostgreSQL health by running a simple SQL query.
  - Network: `kafka-network`.

# ⭕Networks

- `kafka-network`: 
  - Custom bridge network for all services to communicate with each other.

# ⭕Volumes

- `postgres-data`:
  - A local volume used for PostgreSQL data storage.

# ⭕Usage

- Create a directory. example `debezium`
- `cd debezium` to create a `docker-compose.yml` file.
- Copy configuration in notion and edit the request value in `<example>`.
- Paste into the `docker-compose.yml` file.
- Use the command `docker compose up -d` to up your container.
- To create a connector you can create a request in Postman `POST` request with this URI `http://<your host IP:Port or localhost:Port>/connectors`.
- Use Body with row.
- To successfully create a connector, you need to specify the database and table that you already have.
- You find my body to create a connector in my notion.
- After you create a connector it will create a topic for you, you can use it to consume data from your database. Thank you 🙏
