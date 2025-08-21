# MLOps Week 6: Iris Classifier API with Google Kubernetes Engine (GKE) Deployment

## 📋 Project Overview

This project demonstrates a complete **MLOps pipeline** for deploying a machine learning model (Iris flower classifier) as a **REST API** on **Google Kubernetes Engine (GKE)** using **CI/CD automation** with GitHub Actions. The project showcases containerized deployment, automated model downloading from Google Cloud Storage, and production-ready Kubernetes orchestration.

## 🏗️ Project Architecture

```
┌─────────────────┐    ┌──────────────────┐    ┌─────────────────┐
│   GitHub Repo   │───▶│  GitHub Actions  │───▶│  Google Cloud   │
│   (Source Code) │    │   (CI/CD Pipeline)│    │   Infrastructure│
└─────────────────┘    └──────────────────┘    └─────────────────┘
                                │                        │
                                ▼                        ▼
                        ┌──────────────────┐    ┌─────────────────┐
                        │ Docker Registry  │    │ Google Cloud    │
                        │ (Artifact Registry)│   │ Storage (GCS)   │
                        └──────────────────┘    └─────────────────┘
                                │                        │
                                └────────┬───────────────┘
                                         ▼
                                ┌─────────────────┐
                                │ Google          │
                                │ Kubernetes      │
                                │ Engine (GKE)    │
                                │                 │
                                │ ┌─────────────┐ │
                                │ │ Iris API    │ │
                                │ │ (FastAPI)   │ │
                                │ └─────────────┘ │
                                └─────────────────┘
```

## 📁 Project Structure

```
mlops_week6/
├── .github/
│   └── workflows/
│       └── main.yml              # GitHub Actions CI/CD pipeline
├── api/
│   ├── Dockerfile               # Container configuration
│   ├── iris_fastapi.py          # FastAPI application
│   ├── model.joblib            # Pre-trained ML model
│   └── requirements.txt        # Python dependencies
├── deployment.yaml             # Kubernetes deployment configuration
├── .gitignore                 # Git ignore rules
└── key files (encoded)        # GCP service account credentials
```

## 🔧 File Functionalities

### 🚀 Core Application Files

#### `api/iris_fastapi.py`
- **Purpose**: Main FastAPI application serving the Iris classifier
- **Key Features**:
  - RESTful API with Pydantic data validation
  - Model loading on application startup
  - Three endpoints:
    - `GET /`: Welcome message
    - `GET /health`: Health check for Kubernetes probes
    - `POST /predict/`: Iris species prediction endpoint
  - Input validation for sepal/petal measurements
  - Error handling for missing models

#### `api/model.joblib`
- **Purpose**: Pre-trained scikit-learn Decision Tree Classifier
- **Features**: 
  - Trained on the Iris dataset
  - Accepts 4 features: sepal_length, sepal_width, petal_length, petal_width
  - Classifies into 3 species: setosa, versicolor, virginica

#### `api/requirements.txt`
- **Purpose**: Python dependencies specification
- **Dependencies**:
  - `fastapi`: Web framework for building APIs
  - `uvicorn`: ASGI server for FastAPI
  - `scikit-learn`: Machine learning library
  - `numpy`, `pandas`: Data manipulation
  - `joblib`: Model serialization

#### `api/Dockerfile`
- **Purpose**: Container configuration for the API
- **Features**:
  - Based on `python:3.10-slim` for optimal size
  - Installs dependencies and exposes port 8200
  - Runs FastAPI with uvicorn server
  - Production-ready container setup

### ☸️ Kubernetes Configuration

#### `deployment.yaml`
- **Purpose**: Complete Kubernetes deployment configuration
- **Components**:
  1. **Deployment**: 
     - 2 replicas for high availability
     - Rolling update strategy for zero-downtime deployments
     - Resource limits and requests
     - Security context (non-root user)
     - Health probes (readiness, liveness, startup)
  2. **Service**: 
     - LoadBalancer type for external access
     - Port mapping (80 → 8200)
  3. **BackendConfig**: 
     - GKE-specific health check configuration

### 🔄 CI/CD Pipeline

#### `.github/workflows/main.yml`
- **Purpose**: Automated CI/CD pipeline for GKE deployment
- **Workflow Steps**:
  1. **Code Checkout**: Download repository code
  2. **GCP Authentication**: Decode and activate service account
  3. **Model Download**: Fetch trained model from Google Cloud Storage
  4. **GKE Setup**: Configure kubectl for cluster access
  5. **Docker Operations**: Build and push images to Artifact Registry
  6. **Deployment**: Apply Kubernetes configurations
  7. **Verification**: Wait for deployment and get external IP
  8. **Cleanup**: Remove sensitive credentials

- **Environment Variables**:
  - `GCP_PROJECT_ID`: mlopsweek1
  - `GKE_CLUSTER`: mlopsweek6
  - `GKE_REGION`: us-central1
  - `MODEL_BUCKET_URI`: GCS path to the trained model

### 🔐 Security & Configuration

#### `.gitignore`
- **Purpose**: Prevents sensitive files from being committed
- **Protections**:
  - Python cache files and virtual environments
  - API keys and credentials
  - IDE-specific files
  - Temporary and log files

#### Service Account Keys
- **Files**: `new_key`, `new_key1`, `key.json`, etc.
- **Purpose**: Base64-encoded GCP service account credentials
- **Usage**: Enables GitHub Actions to authenticate with Google Cloud services

## 🎯 Final Achievement

### **Complete MLOps Solution Achieved:**

1. **🤖 Model Serving**: 
   - Pre-trained Iris classifier deployed as a REST API
   - Real-time predictions via HTTP endpoints
   - Production-ready FastAPI application

2. **📦 Containerization**: 
   - Docker-based deployment for consistency
   - Lightweight Python container optimized for production

3. **☸️ Kubernetes Orchestration**: 
   - High-availability deployment with 2 replicas
   - Auto-scaling and self-healing capabilities
   - Health monitoring and zero-downtime updates

4. **🔄 CI/CD Automation**: 
   - Fully automated deployment pipeline
   - Triggered on code commits to main branch
   - Model synchronization from Google Cloud Storage

5. **☁️ Cloud Infrastructure**: 
   - Google Kubernetes Engine for container orchestration
   - Google Cloud Storage for model artifacts
   - Google Artifact Registry for container images

6. **🔒 Production Security**: 
   - Non-root container execution
   - Service account-based authentication
   - Secrets management through GitHub Secrets

7. **📊 Monitoring & Health**: 
   - Kubernetes health probes
   - LoadBalancer with external IP access
   - Automated rollout verification

## 🚀 API Usage

Once deployed, the API provides the following endpoints:

- **Health Check**: `GET http://<EXTERNAL_IP>/health`
- **Prediction**: `POST http://<EXTERNAL_IP>/predict/`

### Example Prediction Request:
```json
{
  "sepal_length": 5.1,
  "sepal_width": 3.5,
  "petal_length": 1.4,
  "petal_width": 0.2
}
```

### Example Response:
```json
{
  "predicted_class": "setosa"
}
```

## 🏆 Key MLOps Principles Demonstrated

1. **Infrastructure as Code**: Kubernetes manifests and Dockerfiles
2. **Automated Testing**: Health checks and deployment verification
3. **Continuous Deployment**: Automated pipeline from code to production
4. **Model Versioning**: Model artifacts stored in cloud storage
5. **Scalability**: Kubernetes-based horizontal scaling
6. **Monitoring**: Health probes and service monitoring
7. **Security**: Least privilege access and secure credential management

This project represents a complete, production-ready MLOps pipeline that can serve as a template for deploying machine learning models at scale in cloud environments.
