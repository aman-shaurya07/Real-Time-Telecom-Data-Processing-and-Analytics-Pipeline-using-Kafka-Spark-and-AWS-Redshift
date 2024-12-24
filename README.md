# Real-Time Telecom Data Processing and Analytics Pipeline using Kafka, Spark, and AWS Redshift


## Project Overview
This project implements a real-time data pipeline that ingests simulated telecom call data into a Kafka topic, processes the data using Apache Spark, and stores the processed data into Amazon Redshift. The primary goal of this pipeline is to demonstrate seamless integration between streaming, processing, and data warehousing technologies. 

### Key Components
1. **Apache Kafka**:
   - Acts as the message broker to manage the streaming data.
   - The Kafka Producer script generates telecom data, including caller details, call duration, network providers, and cost, which is published to a topic (`telecom-data`).

2. **Apache Spark**:
   - Consumes real-time data from the Kafka topic using Structured Streaming.
   - Processes the data to ensure quality (e.g., filtering invalid records, ensuring positive call durations) and prepares it for storage.

3. **Amazon Redshift**:
   - A cloud data warehouse that stores the processed data for analytical use cases.
   - Data is written to Redshift using the Spark JDBC connector, with S3 acting as a temporary staging area.

4. **Docker Compose**:
   - Simplifies the setup of Kafka, Zookeeper, Spark, and other required services in a containerized environment.

### Workflow
1. **Data Generation**:
   - The Kafka producer (`kafka_producer.py`) generates telecom call data with random names, durations, and costs.
   - Data is published to the Kafka topic (`telecom-data`).

2. **Streaming and Processing**:
   - Spark Structured Streaming (`spark_streaming_redshift.py`) reads data from the Kafka topic in real-time.
   - It transforms the data (e.g., casting to appropriate types, applying filters) and ensures it adheres to the schema required by Redshift.

3. **Data Storage**:
   - Processed data is written to Amazon Redshift into a table (`telecom.telecom_data`), which is created beforehand using the SQL script (`create_table_redshift.sql`).
   - S3 is used as a staging area for bulk loading.

### Use Cases
- Telecom companies can use this pipeline to analyze call patterns, customer behavior, and network usage in real-time.
- Extensible to other domains like IoT, where real-time data needs to be processed and stored for analysis.

## Prerequisites

1. Docker and Docker Compose installed on your machine.
2. Python 3.x with the following libraries installed:
   - `kafka-python`
   - `psycopg2`
   - `faker`
3. Access to an Amazon Redshift cluster.
4. An S3 bucket for temporary data storage.

## Setup Instructions

### 1. Clone the Repository
```bash
git clone <repository-url>
cd project_root
```

### 2. Start Kafka and Spark Environment
Use Docker Compose to spin up the required services:
```bash
docker-compose up -d
```

### 3. Create the Redshift Table
Connect to your Redshift cluster and execute the SQL commands in `src/create_table_redshift.sql` to create the required schema and table.

### 4. Configure Redshift JDBC Driver
Ensure that the `redshift-jdbc42-2.1.0.12.jar` file is in the `resources/` directory and accessible to the Spark application.

### 5. Start the Kafka Producer
Run the Kafka producer to generate and send data to the `telecom-data` topic:
```bash
python src/kafka_producer.py
```

### 6. (Optional for practice) Test Redshift Connection
Use the script `redshift_test_connection.py` to verify connectivity to your Redshift cluster:
```bash
python src/redshift_test_connection.py
```

### 7. Run the Spark Streaming Job
Start the Spark job to process data from Kafka and write to Redshift:
```bash
spark-submit --jars resources/redshift-jdbc42-2.1.0.12.jar src/spark_streaming_redshift.py
```

## Troubleshooting

### Common Issues and Fixes

1. **Kafka Services Not Starting**:
   - Check if Docker is running.
   - Use `docker ps` to verify running containers.

2. **Redshift Connection Issues**:
   - Verify the Redshift endpoint, port, and credentials in `redshift_test_connection.py`.
   - Ensure your IP is whitelisted in the Redshift security group.

3. **Spark Job Errors**:
   - Check if the Kafka and Redshift dependencies are correctly specified in the Spark session.
   - Verify the S3 bucket configuration for temporary data storage.

## Additional Notes
- The project is designed for educational purposes and demonstrates integration between Kafka, Spark, and Redshift.
- Modify the configurations in the scripts to suit your specific environment.
