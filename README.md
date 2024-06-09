# Data-Engineering

# Realtime Data Streaming | End-to-End Data Engineering Project

## Introduction

This project serves as a comprehensive guide to building an end-to-end data engineering pipeline. It covers each stage from data ingestion to processing and finally to storage, utilizing a robust tech stack that includes Apache Airflow, Python, Apache Kafka, Apache Zookeeper, Apache Spark, and Cassandra. Everything is containerized using Docker for ease of deployment and scalability.

## System Architecture

![System Architecture](https://github.com/airscholar/e2e-data-engineering/blob/main/Data%20engineering%20architecture.png)

The project is designed with the following components:

- **Data Source**: We use `randomuser.me` API to generate random user data for our pipeline.
- **Apache Airflow**: Responsible for orchestrating the pipeline and storing fetched data in a PostgreSQL database.
- **Apache Kafka and Zookeeper**: Used for streaming data from PostgreSQL to the processing engine.
- **Control Center and Schema Registry**: Helps in monitoring and schema management of our Kafka streams.
- **Apache Spark**: For data processing with its master and worker nodes.
- **Cassandra**: Where the processed data will be stored.

## What You'll Learn

- Setting up a data pipeline with Apache Airflow
- Real-time data streaming with Apache Kafka
- Distributed synchronization with Apache Zookeeper
- Data processing techniques with Apache Spark
- Data storage solutions with Cassandra and PostgreSQL
- Containerizing your entire data engineering setup with Docker

## Technologies

- Apache Airflow
- Python
- Apache Kafka
- Apache Zookeeper
- Apache Spark
- Cassandra
- PostgreSQL
- Docker

## Getting Started

1. Clone the repository:
    ```bash
    git clone https://github.com/airscholar/e2e-data-engineering.git
    ```

2. Navigate to the project directory:
    ```bash
    cd e2e-data-engineering
    ```

3. Run Docker Compose to spin up the services:
    ```bash
    docker-compose up
    ```


#Step-by-Step Guide

#Step 1: Folder Structure and Installation
The folder structure for this project is 2 folders and 5 files with the structure and names below:

- ├── dags
- │   └── kafka_stream.py
- ├── docker-compose.yml
- ├── requirements.txt
- ├── script
- │   └── entrypoint.sh
- └── spark_stream.py

2 directories, 5 files
And the requirements.txt file with the required dependencies are as shown below. 
You should run 
```pip install -r requirements.txt```
to install the dependencies.


Entrypoint.sh file
The entrypoint.sh file contains the commands to be executed once the attached container has been initialised. To make it work as expected, it is advised you run chmod +x scripts/entrypoint.sh in the root directory to convert the script to executable.


##Step 2: Setting up Docker Containers

Firstly, we use Docker Compose to spin up containers for the project. The dockerfile.yml below creates the following containers:

Zookeeper: Zookeeper acts as a distributed configuration and synchronization service. It’s particularly vital for Apache Kafka to manage its distributed nature.

Apache Kafka Broker: Kafka Broker handles the storage, retrieval, and transfer of messages (records). It’s a part of the Kafka distributed streaming platform.

Schema Registry: The Schema Registry provides a serving layer for your metadata. It is a RESTful interface for storing and retrieving Avro schemas which is particularly useful when your Kafka streams need to understand the schema of the records.

Control Center: This is a web-based interface for managing and monitoring your Kafka environments. It provides features like data inspection, topic creation, and setting up Kafka Connect.

Spark Master: Spark Master is the point of entry to any Spark functionality. It is responsible for distributing work across the Spark Cluster.

Spark Worker: Spark Worker is responsible for executing the tasks that the master node assigns it and returning the computed results.

Cassandra DB: Cassandra is a NoSQL database designed to handle large amounts of data across many nodes without any single point of failure. It’s ideal for high-velocity data like streaming data.

Apache Airflow Webserver: Apache Airflow’s web server is the interface where you will define and monitor your workflows (DAGs). Airflow is commonly used for orchestrating complex ETL tasks.

Scheduler: The Scheduler in Airflow triggers the tasks and constructs the data pipelines. It ensures tasks are executed at the right time or when triggered by other tasks.

PostgresDB: PostgreSQL is a relational database. It’s used here as the metadata database for Apache Airflow and can also serve as a general-purpose data store.

Below is the content of the dockerfile.yml used to orchestrate the spinning up process.

Attention: The AIRFLOW__WEBSERVER__SECRET_KEY must be the same for the webserver and the scheduler for the containers to work effectively.


To spin the containers up, run the script below:

```docker compose up -d```

starting up the containers

##Step 3: Streaming Data into Apache Kafka

Once the docker containers are up and running, create a new a file in the dags directory (stream_kafka.py), here is it’s content:


Goto the airflow UI on localhost:8080, and run the user_automation dag (the play button in the actions section).


To see the data streams on the control center UI, goto localhost:9021


control center landing page

users_created topic

viewing users created topic data
Step 4: Streaming Data into Cassandra
In the root directory, create a new file called spark_stream.py and add the following content to it.


Then run this script in your root directory to submit the job to your spark cluster:

```spark-submit --master spark://localhost:7077 spark_stream.py```
If you goto localhost:9090 you should see the spark master and worker setup and the worker state is ALIVE.


Now, if you want to see the data on cassandra, you need to connect to the cluster using:

```docker exec -it cassandra cqlsh -u cassandra -p cassandra localhost 9042```

When you run DESCRIBE spark_streams.created_users; you should see the keyspace and table created successfully.


To get the data streamed into cassandra, run:

```SELECT * FROM spark_streams.created_users;```





