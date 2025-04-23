# smol-course


RAG Pipeline
This repository contains a Retrieval-Augmented Generation (RAG) pipeline that uses Milvus as a vector database and Phoenix for monitoring.
Prerequisites

Git
Python 3.8+
Docker and Docker Compose
pip

Installation
1. Clone the Repository
git clone <repository-url>
cd <repository-directory>

2. Create a Virtual Environment
python -m venv venv

# Activate the virtual environment
# On Windows
venv\Scripts\activate
# On macOS/Linux
source venv/bin/activate

3. Install Python Dependencies
pip install -r requirements.txt

4. Set Up Docker Containers
Install Milvus
Milvus provides a Docker Compose configuration file for easy installation:
# Download the configuration file
wget https://github.com/milvus-io/milvus/releases/download/v2.5.10/milvus-standalone-docker-compose.yml -O docker-compose.yml

4. Set Up Docker Containers
Install Milvus
To install Milvus, follow these steps:

Start Milvus using Docker Compose:

sudo docker compose up -d

Note:

If you fail to run the above command, check whether your system has Docker Compose V1 installed. If so, you are advised to migrate to Docker Compose V2.
If you encounter any issues pulling the image, contact community@zilliz.com with details about the problem for support.

After starting Milvus:

Containers named milvus-standalone, milvus-minio, and milvus-etcd are up.
The milvus-etcd container does not expose any ports to the host and maps its data to volumes/etcd in the current folder.
The milvus-minio container serves ports 9090 and 9091 locally with the default authentication credentials and maps its data to volumes/minio in the current folder.
The milvus-standalone container serves port 19530 locally with the default settings and maps its data to volumes/milvus in the current folder.

To check if the containers are up and running, use:
sudo docker-compose ps

You can also access the Milvus WebUI at http://127.0.0.1:9091/webui/ to learn more about your Milvus instance.
Install Phoenix
There are two ways to install Phoenix:
Option 1: Simple Docker Container

Pull the Phoenix image:

docker pull arizephoenix/phoenix


Run the Phoenix container:

docker run -p 6006:6006 -p 4317:4317 -i -t arizephoenix/phoenix:latest

After installation, you can access Phoenix at http://0.0.0.0:6006.
Option 2: Using Docker Compose (Recommended for Production)

Create a phoenix-compose.yaml file with the following content:

services:
  phoenix:
    image: arizephoenix/phoenix:latest
    ports:
      - "6006:6006"  # UI and OTLP HTTP collector
      - "4317:4317"  # OTLP gRPC collector
  backend:
    build:
      context: ./backend
      dockerfile: Dockerfile
      args:
        OPENAI_API_KEY: ${OPENAI_API_KEY}
    ports:
      - "8000:8000"
    environment:
      - OPENAI_API_KEY=${OPENAI_API_KEY}
      - COLLECTOR_ENDPOINT=http://phoenix:6006/v1/traces
      - PROD_CORS_ORIGIN=http://localhost:3000
      # Set INSTRUMENT_LLAMA_INDEX=false to disable instrumentation
      - INSTRUMENT_LLAMA_INDEX=true
    healthcheck:
      test: ["CMD", "wget", "--spider", "http://0.0.0.0:8000/api/chat/healthcheck"]
      interval: 5s
      timeout: 1s
      retries: 5
  frontend:
    build: frontend
    ports:
      - "3000:3000"
    depends_on:
      backend:
        condition: service_healthy


Start Phoenix and associated services:

docker compose -f phoenix-compose.yaml up -d

This ensures you always have a running Phoenix instance alongside your application.
Note: If Phoenix fails to run in Docker, uncomment the line session = px.launch_app() (line 20) in api.py to launch the app manually.
5. Install Attu (Milvus Management UI)
To install and start Attu, follow these steps:
Determine your IPv4 address
Run the following command to get your IPv4 address:
ipconfig

Look for the IPv4 address in the output (e.g., 192.168.1.100).
Start the Attu container
You can start the Attu container using one of the following methods:
Method 1: Using Docker network
If you are running Milvus and Attu on the same Docker network, use:
docker run --name attu --network milvus -p 8000:3000 -e MILVUS_URL=milvus-standalone:19530 zilliz/attu:v2.3.6

Method 2: Using IPv4 address
If the above command does not work, or if you are accessing Attu from a different machine, use:
docker run --name attu -p 8000:3000 -e HOST_URL=http://xxx.xxx.x.x:8000 -e MILVUS_URL=http://xxx.xxx.x.x:19530 zilliz/attu:v2.3.6

Replace xxx.xxx.x.x with your actual IPv4 address.
Access Attu
After starting the container, you can access Attu at http://localhost:8000 (or http://xxx.xxx.x.x:8000 if using Method 2).
Subsequent launches
For subsequent launches, you can start the container with:
docker start attu



6. Start the API Server
uvicorn api:app --reload

The API server will be available at http://localhost:8000.
Usage
When you start the API server using uvicorn api:app --reload, a chatbot UI will be accessible at:
uvicorn api:app --reload --port 8001

http://localhost:8000
You can interact with the RAG pipeline directly through this user interface by entering your queries.
Configuration
Configuration options can be modified in the config.py file.
Troubleshooting

If you encounter issues with Milvus or Phoenix, check that the Docker containers are running correctly with docker ps.
For API server issues, check the logs for error messages.

Stopping and Deleting Containers
When you're done, you can stop and delete the containers:
# Stop Milvus
sudo docker compose down

# Stop Phoenix (if using docker-compose)
docker compose -f phoenix-compose.yaml down

# OR stop Phoenix (if using single container)
docker stop $(docker ps -q --filter "ancestor=arizephoenix/phoenix")

# Stop Attu
docker stop attu

# Delete service data
sudo rm -rf volumes

Note: To stop the Attu container, use docker stop attu. If you want to remove the container entirely, use docker rm attu after stopping it.
License
[Your license information here]







# RAG Pipeline

This repository contains a Retrieval-Augmented Generation (RAG) pipeline that uses Milvus as a vector database and Phoenix for monitoring.

## Prerequisites

- Git
- Python 3.8+
- Docker and Docker Compose
- pip

## Installation

### 1. Clone the Repository

```bash
git clone <repository-url>
cd <repository-directory>
```

### 2. Create a Virtual Environment

```bash
python -m venv venv

# Activate the virtual environment
# On Windows
venv\Scripts\activate
# On macOS/Linux
source venv/bin/activate
```

### 3. Install Python Dependencies

```bash
pip install -r requirements.txt
```

### 4. Set Up Docker Containers

#### Install Milvus

Milvus provides a Docker Compose configuration file for easy installation:

```bash
# Download the configuration file
wget https://github.com/milvus-io/milvus/releases/download/v2.5.10/milvus-standalone-docker-compose.yml -O docker-compose.yml

# Start Milvus
sudo docker compose up -d
```

**Note:** 
* If you failed to run the above command, please check whether your system has Docker Compose V1 installed. If this is the case, you are advised to migrate to Docker Compose V2.
* If you encounter any issues pulling the image, contact community@zilliz.com with details about the problem for support.

After starting up Milvus:
* Containers named **milvus-standalone**, **milvus-minio**, and **milvus-etcd** are up.
   * The **milvus-etcd** container does not expose any ports to the host and maps its data to **volumes/etcd** in the current folder.
   * The **milvus-minio** container serves ports **9090** and **9091** locally with the default authentication credentials and maps its data to **volumes/minio** in the current folder.
   * The **milvus-standalone** container serves ports **19530** locally with the default settings and maps its data to **volumes/milvus** in the current folder.

You can check if the containers are up and running using:

```bash
sudo docker-compose ps
```

You can also access Milvus WebUI at `http://127.0.0.1:9091/webui/` to learn more about your Milvus instance.

#### Install Phoenix

There are two ways to install Phoenix:

**Option 1: Simple Docker Container**

Pull the Phoenix image:

```bash
docker pull arizephoenix/phoenix
```

Run the Phoenix container:

```bash
docker run -p 6006:6006 -p 4317:4317 -i -t arizephoenix/phoenix:latest
```

After installation, you can access Phoenix at http://0.0.0.0:6006.

**Option 2: Using Docker Compose (Recommended for Production)**

Create a `phoenix-compose.yaml` file:

```yaml
services:
  phoenix:
    image: arizephoenix/phoenix:latest
    ports:
      - "6006:6006"  # UI and OTLP HTTP collector
      - "4317:4317"  # OTLP gRPC collector
  backend:
    build:
      context: ./backend
      dockerfile: Dockerfile
      args:
        OPENAI_API_KEY: ${OPENAI_API_KEY}
    ports:
      - "8000:8000"
    environment:
      - OPENAI_API_KEY=${OPENAI_API_KEY}
      - COLLECTOR_ENDPOINT=http://phoenix:6006/v1/traces
      - PROD_CORS_ORIGIN=http://localhost:3000
      # Set INSTRUMENT_LLAMA_INDEX=false to disable instrumentation
      - INSTRUMENT_LLAMA_INDEX=true
    healthcheck:
      test: ["CMD", "wget", "--spider", "http://0.0.0.0:8000/api/chat/healthcheck"]
      interval: 5s
      timeout: 1s
      retries: 5
  frontend:
    build: frontend
    ports:
      - "3000:3000"
    depends_on:
      backend:
        condition: service_healthy
```

Start Phoenix and associated services:

```bash
docker compose -f phoenix-compose.yaml up -d
```

This ensures you always have a running Phoenix instance alongside your application.

**Note:** If Phoenix fails to run in Docker, uncomment the line `session = px.launch_app()` (line 20) in api.py to launch the app manually.

### 5. Install Attu (Milvus Management UI)

```bash
docker run --name attu --network milvus -p 8000:3000 -e MILVUS_URL=milvus-standalone:19530 zilliz/attu:v2.3.6
```
docker start attu
After installation, you can access Attu at http://localhost:8000.

### 6. Start the API Server

```bash
uvicorn api:app --reload --port 8001
```

The API server will be available at http://localhost:8001.

## Usage

When you start the API server using `uvicorn api:app --reload`, a chatbot UI will be accessible at:

```
http://localhost:8000
```

You can interact with the RAG pipeline directly through this user interface by entering your queries.



## Configuration

Configuration options can be modified in the `config.py` file.

## Troubleshooting

- If you encounter issues with Milvus or Phoenix, check that the Docker containers are running correctly with `docker ps`.
- For API server issues, check the logs for error messages.

## Stopping and Deleting Containers
When you're done, you can stop and delete the containers:
```
Stop Milvus
sudo docker compose down
```

## Stop Phoenix (if using docker-compose)
```
docker compose -f phoenix-compose.yaml down
```
## OR stop Phoenix (if using single container)
```
docker stop $(docker ps -q --filter "ancestor=arizephoenix/phoenix")
```
## Delete service data
```
sudo rm -rf volumes

## License

[Your license information here]






