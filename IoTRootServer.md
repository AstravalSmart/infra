# 🚀 Project Server Setup Guide

This document explains how to set up a fresh **Ubuntu server** with all necessary dependencies and run the IoT service stack using **Docker Compose**.

---

## 📦 Services in the Stack

The stack runs the following containers:

* **PostgreSQL 16** → main SQL database
* **MongoDB 7** → NoSQL database
* **VerneMQ** → MQTT broker
* **pgAdmin 4** → PostgreSQL management UI

---

## 🛠️ Step 1: Update and Install Dependencies

Run the following commands on your fresh Ubuntu server:

```bash
# Update system packages
sudo apt update && sudo apt upgrade -y

# Install basic tools
sudo apt install -y curl wget git unzip ufw

# Install Docker
curl -fsSL https://get.docker.com | sudo bash

# Enable and start Docker service
sudo systemctl enable docker
sudo systemctl start docker

# Install Docker Compose (v2.x+)
sudo curl -L "https://github.com/docker/compose/releases/download/v2.29.2/docker-compose-linux-x86_64" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose

# Verify installation
docker --version
docker-compose --version
```

---

## 📂 Step 2: Prepare Project Directory

```bash
# Create a project folder
mkdir ~/iot-server && cd ~/iot-server

# Create a file for VerneMQ password
touch vernemq_passwd

# Generate password file for VerneMQ (replace USERNAME with your MQTT user)
docker run --rm -v $(pwd):/vernemq erlio/docker-vernemq:latest vmq-passwd -c /vernemq/vernemq_passwd USERNAME
```

> 💡 You can add multiple users with:
> `docker run --rm -v $(pwd):/vernemq erlio/docker-vernemq:latest vmq-passwd /vernemq/vernemq_passwd NEWUSER`

---

## 📑 Step 3: Create `docker-compose.yml`

Save the following content into a file named **`docker-compose.yml`** inside your project directory:

```yaml
version: '3.9'

services:
  postgres:
    image: postgres:16
    container_name: postgres
    restart: unless-stopped
    environment:
      POSTGRES_USER: iot_user
      POSTGRES_PASSWORD: StrongPostgresPass123!
      POSTGRES_DB: iot_db
    volumes:
      - pgdata:/var/lib/postgresql/data
    networks:
      - iot-network

  mongodb:
    image: mongo:7
    container_name: mongodb
    restart: unless-stopped
    environment:
      MONGO_INITDB_ROOT_USERNAME: iot_admin
      MONGO_INITDB_ROOT_PASSWORD: StrongMongoPass123!
      MONGO_INITDB_DATABASE: iot_db
    volumes:
      - mongodata:/data/db
    networks:
      - iot-network

  vernemq:
    image: erlio/docker-vernemq:latest
    container_name: vernemq
    restart: unless-stopped
    environment:
      DOCKER_VERNEMQ_ALLOW_ANONYMOUS: "off"
      VERNEMQ_LISTENER__TCP: 1883
      VERNEMQ_LISTENER__WS: 8080
      VERNEMQ_PASSWORD_FILE: /vernemq/passwd
    volumes:
      - vernemqdata:/vernemq
      - ./vernemq_passwd:/vernemq/passwd:ro
    networks:
      - iot-network
    ports:
      - "1883:1883"
      - "8080:8080"
      - "4369:4369"
      - "44053:44053"

  pgadmin:
    image: dpage/pgadmin4
    container_name: pgadmin
    restart: unless-stopped
    environment:
      PGADMIN_DEFAULT_EMAIL: admin@iotroot.com
      PGADMIN_DEFAULT_PASSWORD: StrongPgAdminPass123!
    ports:
      - "5050:80"
    networks:
      - iot-network
    volumes:
      - pgadmin_data:/var/lib/pgadmin

volumes:
  pgdata:
  mongodata:
  vernemqdata:
  pgadmin_data:

networks:
  iot-network:
    driver: bridge
```

---

## ▶️ Step 4: Start the Services

```bash
# Run in detached mode
docker-compose up -d

# Check running containers
docker ps
```

---

## 🔒 Step 5: Secure the Server (Recommended)

```bash
# Enable UFW firewall
sudo ufw allow OpenSSH
sudo ufw allow 1883/tcp    # MQTT
sudo ufw allow 8080/tcp    # WebSocket for MQTT
sudo ufw allow 5050/tcp    # pgAdmin
sudo ufw enable
```

---

## 🌐 Service Access

* **PostgreSQL** → inside Docker network (`postgres:5432`)
* **MongoDB** → inside Docker network (`mongodb:27017`)
* **MQTT Broker (VerneMQ)** → `mqtt://<SERVER_IP>:1883`
* **WebSocket MQTT** → `ws://<SERVER_IP>:8080`
* **pgAdmin** → http://`<SERVER_IP>`:5050

---

## ✅ Useful Commands

```bash
# Stop services
docker-compose down

# Restart services
docker-compose restart

# View logs
docker-compose logs -f

# Remove volumes (⚠️ deletes all data!)
docker-compose down -v
```

---

Would you like me to also include a **section for automated backup strategy** (Postgres & Mongo) in this guide, or keep it as just setup instructions?
