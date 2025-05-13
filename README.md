# Airflow Data Pipeline with Great Expectations

This repository demonstrates building an advanced data pipeline with data quality checks using Apache Airflow and Great Expectations. The project showcases a real-world ML pipeline for mobility-as-a-service vendors to process ride data, validate it, train models to estimate ride duration, and decide if models are suitable for deployment.

## Key Learning Points

### 1. Airflow Concepts and Implementation
- **Taskflow API**: Implemented DAGs using Airflow's Taskflow API for cleaner, more Pythonic workflow definitions
- **BranchPythonOperator**: Used for conditional task execution based on model performance metrics
- **DummyOperator**: Implemented start and end tasks to mark the beginning and end of workflows
- **Trigger Rules**: Modified default trigger rules (using `none_failed_or_skipped`) to control task execution flow
- **Airflow Variables**: Used to store and retrieve configuration data like bucket names

### 2. Data Quality with Great Expectations
- **Quality Dimensions**: Explored accuracy, completeness, consistency, validity, uniqueness, and timeliness
- **GreatExpectationsOperator**: Integrated data validation into the Airflow pipeline
- **Expectation Suites**: Defined quality requirements for ride data (e.g., passenger count validation)
- **Data Validation**: Implemented checks to ensure data meets business requirements before model training

### 3. ML Model Training and Evaluation
- **Preprocessing**: Worked with pre-processed data partitioned by vendor
- **Model Training**: Implemented a linear regression model to predict ride duration
- **Evaluation**: Used RMSE (Root Mean Square Error) to evaluate model performance
- **Decision Making**: Created logic to decide whether to deploy models based on performance metrics

### 4. Dynamic DAG Generation
- **DRY Principle**: Applied "Don't Repeat Yourself" to avoid code duplication across similar DAGs
- **Jinja Templates**: Created template DAGs with placeholders for dynamic elements
- **Configuration Files**: Used JSON configuration to specify vendor-specific parameters
- **Dynamic Generation**: Built a script to generate multiple DAGs from a single template

### 5. Best Practices for Airflow
- **Task Dependencies**: Defined clear task dependencies using the `>>` operator and proper `TaskFlow` syntax
- **Error Handling**: Implemented appropriate error handling and failure scenarios
- **Separation of Concerns**: Distinguished between orchestration (Airflow's role) and actual data processing
- **Parameterization**: Created flexible, reusable workflows that adapt to different vendors and requirements

## Project Structure
- **src/**: Contains the pipeline implementation
  - **dags/**: Airflow DAG definitions for each vendor
  - **templates/**: Template files for dynamic DAG generation
    - **dag_configs/**: JSON configuration files for each vendor
- **data/**: Contains training and test datasets for each vendor
- **images/**: Documentation images
- **scripts/**: Utility scripts

## Vendors
The pipeline was implemented for three fictitious mobility service vendors:
1. **Easy Destiny**: Successfully deploys the model
2. **Alitran**: Model training succeeds but performance is below deployment threshold
3. **ToMyPlaceAI**: Data fails quality validation checks

## Note
This implementation focuses on educational purposes. In production environments:
1. Airflow should delegate processing to appropriate tools (databases, Spark clusters)
2. Heavy computation should not be performed within the Airflow worker
3. Operators specifically designed for storage/processing solutions should be used

## Setup and Execution
The repository is configured to run in an AWS environment with:
- S3 buckets for DAG storage and raw data
- EC2 instance running dockerized Airflow
- CloudFormation stack for infrastructure management

Each DAG follows the workflow:
1. Start task
2. Data quality validation
3. Model training & evaluation (if quality checks pass)
4. Deployment decision based on model performance
5. Either deploy model or notify about low performance
6. End task 