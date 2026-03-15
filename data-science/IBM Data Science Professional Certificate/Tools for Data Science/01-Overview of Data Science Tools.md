# Module 1 — Data Science Tools Overview

Data science involves several stages that transform **raw data into insights and predictive models**.  
These stages require specialized tools that support the **data science lifecycle**.

## Main Data Science Tasks

A typical data science workflow includes the following tasks:

- **Data Management** — collecting, storing, and retrieving data
- **Data Integration & Transformation (ETL/ELT)** — preparing and cleaning data
- **Data Visualization** — exploring and communicating insights
- **Model Building** — training machine learning models
- **Model Deployment** — integrating models into real applications
- **Model Monitoring & Assessment** — evaluating model performance and reliability

These tasks are supported by additional infrastructure tools such as:

- **Code asset management** (version control)
- **Data asset management** (datasets and metadata)
- **Development environments**
- **Execution environments**

---

# Open Source Tools for Data Science

Open-source tools form the **core ecosystem of modern data science** because they are flexible, extensible, and widely supported by the community.

## Data Management Tools

These tools store and manage structured or unstructured data.

**Relational Databases**

- **MySQL** – widely used open-source relational database system.
- **PostgreSQL** – advanced relational database with strong support for complex queries and data integrity.

**NoSQL Databases**

- **MongoDB** – document-oriented database storing data in JSON-like documents.
- **Apache CouchDB** – distributed NoSQL database designed for reliability and scalability.
- **Apache Cassandra** – highly scalable distributed database designed for handling large volumes of data.

**Distributed Storage Systems**

- **Hadoop File System (HDFS)** – distributed storage system used in big data environments.
- **Ceph** – scalable distributed storage system for object, block, and file storage.

**Search Engine Storage**

- **Elasticsearch** – distributed search and analytics engine commonly used for log analysis and full-text search.

---

## Data Integration and Transformation Tools (ETL / ELT)

These tools help **collect, process, and transform data pipelines**.

- **Apache Airflow** – workflow orchestration tool used to schedule and monitor data pipelines.
- **Kubeflow** – platform for running machine learning pipelines on Kubernetes.
- **Apache Kafka** – distributed streaming platform used for real-time data pipelines.
- **Apache NiFi** – visual data flow tool for automating data movement between systems.
- **Apache Spark SQL** – distributed SQL engine built on Apache Spark for large-scale data processing.
- **Node-RED** – flow-based development tool for integrating APIs, hardware, and services.

---

## Data Visualization Tools

These tools help **analyze and communicate insights visually**.

- **PixieDust** – Python library that simplifies data exploration and visualization inside Jupyter notebooks.
- **Hue** – web interface used for querying and visualizing data in big data environments.
- **Kibana** – visualization platform designed to work with Elasticsearch data.
- **Apache Superset** – modern data exploration and visualization platform for interactive dashboards.

---

## Model Deployment Tools

These tools make machine learning models available in **production systems**.

- **Apache PredictionIO** – open-source machine learning server for deploying predictive models.
- **Seldon** – platform for deploying and monitoring machine learning models at scale.
- **Kubernetes** – container orchestration system used to manage scalable model deployments.
- **MLeap** – library for deploying SparkML models in real-time applications.
- **TensorFlow Serving** – production system for serving machine learning models.
- **TensorFlow Lite** – lightweight framework for deploying models on mobile and embedded devices.
- **TensorFlow.js** – allows machine learning models to run directly in web browsers.

---

## Model Monitoring and Assessment Tools

These tools track the **performance, fairness, and robustness of deployed models**.

- **ModelDB** – system for managing machine learning model metadata.
- **Prometheus** – monitoring system used for tracking system and model metrics.
- **IBM AI Fairness 360** – toolkit for detecting and mitigating bias in machine learning models.
- **IBM Adversarial Robustness 360** – toolkit for improving model robustness against adversarial attacks.
- **IBM AI Explainability 360** – toolkit for explaining predictions made by machine learning models.

---

## Code Asset Management

These tools manage **source code versions and collaboration**.

- **Git** – distributed version control system.
- **GitHub** – web platform for hosting Git repositories and collaborative development.
- **GitLab** – open-source alternative to GitHub with built-in CI/CD.
- **Bitbucket** – Git repository hosting service with team collaboration features.

---

## Data Asset Management / Data Governance

These tools organize **datasets, metadata, and data lineage**.

- **Apache Atlas** – metadata management and data governance platform.
- **ODPi Egeria** – open metadata and governance framework.
- **Kylo** – platform for managing and governing large-scale data pipelines.

---

# Development and Execution Environments

These tools provide environments for **writing, running, and analyzing code**.

## Interactive Development Environments

**Jupyter Notebook**
- Interactive computing environment combining code, text, and visualizations.
- Supports **100+ programming languages** through kernels.

**JupyterLab**
- Next-generation interface for Jupyter.
- Allows working with notebooks, terminals, and datasets in one workspace.

**Apache Zeppelin**
- Notebook-based environment with built-in visualization capabilities.

**RStudio**
- Integrated development environment focused on **R programming**.

**Spyder**
- Python IDE designed for scientific computing and data analysis.

---

## Distributed Processing Frameworks

These systems allow processing **large datasets across clusters**.

- **Apache Spark** – distributed data processing engine widely used in big data systems.
- **Apache Flink** – framework optimized for **real-time stream processing**.
- **Ray** – distributed execution framework for **large-scale machine learning workloads**.

---

# Visual Data Science Tools (Low-Code)

These tools allow building data science workflows with minimal coding.

**KNIME**
- Visual workflow platform using drag-and-drop components
- Supports data transformation, visualization, and machine learning
- Integrates with Python, R, and Apache Spark

**Orange**
- Visual data analysis and machine learning platform
- Focuses on simplicity and rapid experimentation

---

# Commercial and Cloud Tools (Overview)

Some organizations use commercial or cloud platforms that integrate multiple data science tasks.

Examples include:

- **Tableau, Power BI, Cognos Analytics** – business intelligence and visualization tools
- **SPSS Modeler, SAS Enterprise Miner** – commercial machine learning tools
- **Watson Studio, Azure Machine Learning** – integrated cloud platforms
- **Amazon DynamoDB, IBM Db2 Cloud** – managed cloud databases

These platforms provide **enterprise support, scalability, and integrated workflows** for large organizations.
