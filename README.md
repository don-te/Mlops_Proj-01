```markdown
# 🚗 Vehicle Insurance MLOps Pipeline

[![Python Version](https://img.shields.io/badge/Python-3.12-blue.svg)](https://www.python.org/)
[![Database](https://img.shields.io/badge/Database-MongoDB%20Atlas-green.svg)](https://www.mongodb.com/cloud/atlas)
[![Cloud Provider](https://img.shields.io/badge/Cloud-AWS%20(S3%20%7C%20ECR%20%7C%20EC2)-orange.svg)](https://aws.amazon.com/)
[![CI/CD](https://img.shields.io/badge/CI%2FCD-GitHub%20Actions-black.svg)](https://github.com/features/actions)
[![Containerization](https://img.shields.io/badge/Container-Docker-blue.svg)](https://www.docker.com/)

An end-to-end production-grade MLOps pipeline designed to ingest vehicle insurance data, execute scalable transformation workflows, evaluate model performance barriers, and systematically deploy a responsive FastAPI application to AWS using robust CI/CD practices.

---

## 🏗️ Architectural Framework

This project implements a modular, clean-code architecture based on the enterprise-standard **Configuration-Entity-Component** pattern.

```text
src/
├── constants/          # Centralized hyperparameter and environment definitions
├── config/             # Schema validation and infrastructure configuration
├── entity/             # Data models (config_entity & artifact_entity)
├── components/         # Independent step-executors (Ingestion -> Deployment)
└── pipeline/           # Orchestrated execution flows (Training & Prediction)

```

### 📈 Production Component Lifecycle

Every stage of the machine learning lifecycle executes a structured workflow to maximize isolation and repeatability:

1. **Data Ingestion:** Seamlessly connects to **MongoDB Atlas**, extracts large-scale operational records dynamically, and converts BSON key-value structures into standardized Pandas DataFrames.
2. **Data Validation:** Checks data drift and schema health against a strict blueprint configuration defined in `schema.yaml`.
3. **Data Transformation:** Standardizes features, handles encoding via custom pipeline estimators, and saves serialized preprocessor objects.
4. **Model Training & Evaluation:** Trains standard models and checks updates against a strict production drift threshold (**Threshold Delta: 0.02**). If performance beats the active champion model, it is promoted.
5. **Model Pusher:** Encapsulates the verified model state and synchronizes structural artifacts to a secure **AWS S3 Model Registry Bucket** (`my-model-mlopsproj`).

---

## 🛠️ Tech Stack & Cloud Services

| Pillar | Technology Used | Implementation Purpose |
| --- | --- | --- |
| **Database Layer** | MongoDB Atlas | Distributed document store hosting insurance records (`381k+` rows). |
| **Cloud Storage** | AWS S3 | Cloud asset store functioning as the secure, persistent **Model Registry**. |
| **Compute / Host** | AWS EC2 (Ubuntu) | Scalable runtime host provisioning runtime endpoints via custom TCP inbound configurations. |
| **Containerization** | Docker | Packaging runtime configurations, dependencies, and code layers safely. |
| **Container Registry** | AWS ECR | Secure repository hosting production-ready private Docker Images (`vehicleproj`). |
| **CI/CD Automation** | GitHub Actions | Automated build triggers pushing builds to a **Self-Hosted Runner**. |

---

## 🚀 Local Development Setup

### 1. Environment Initialization

Initialize a dedicated virtual environment and install project distributions:

```bash
# Create and initialize the project virtual environment
conda create -n vehicle python=3.12 -y
conda activate vehicle

# Install project distribution details and application requirements
pip install -r requirements.txt

# Verify local package installation
pip list

```

### 2. Infrastructure Configuration

Configure infrastructure connection strings securely via environment variables.

#### For Linux/macOS (Bash):

```bash
export MONGODB_URL="mongodb+srv://<username>:<password>@cluster0.mongodb.net/..."
export AWS_ACCESS_KEY_ID="YOUR_AWS_ACCESS_KEY"
export AWS_SECRET_ACCESS_KEY="YOUR_AWS_SECRET_KEY"

```

#### For Windows (PowerShell):

```powershell
$env:MONGODB_URL="mongodb+srv://<username>:<password>@cluster0.mongodb.net/..."
$env:AWS_ACCESS_KEY_ID="YOUR_AWS_ACCESS_KEY"
$env:AWS_SECRET_ACCESS_KEY="YOUR_AWS_SECRET_KEY"

```

*(Alternatively, set these via Windows Advanced System Settings -> Environment Variables).*

### 3. Pipeline Execution

Execute validation steps or test component configurations end-to-end via:

```bash
python demo.py

```

---

## 🌐 Production Deployment (CI/CD Architecture)

This pipeline features a continuous delivery workflow utilizing an isolated **GitHub Actions Self-Hosted Runner** operating directly on an AWS EC2 instance.

```text
[Local Git Push] ──> [GitHub Repository] ──> [Triggers Action Workflow]
                                                      │
     ┌────────────────────────────────────────────────┴───────────────────────────────┐
     ▼                                                                                ▼
[Build Docker Image]                                                        [Runner Signals AWS EC2]
     │                                                                                │
     ▼                                                                                ▼
[Push Image to AWS ECR] ───────────────────────────────────────────────────> [Pull & Run Container]
                                                                                      │
                                                                                      ▼
                                                                           [App Live on Port 5080]

```

### GitHub Repository Secrets

To support secure image builds and AWS integrations, ensure the following repository secrets are mapped under `Settings > Secrets and variables > Actions`:

* `AWS_ACCESS_KEY_ID`
* `AWS_SECRET_ACCESS_KEY`
* `AWS_DEFAULT_REGION` (e.g., `us-east-1`)
* `ECR_REPO` (e.g., `vehicleproj`)

### Live Application Verification

Once the automated pipeline builds successfully, access your inference endpoint globally using your EC2 instance's public IP address:

* **User Endpoint Portal:** `http://<YOUR_EC2_PUBLIC_IP>:5080/`
* **Trigger Model Retraining Flow:** `http://<YOUR_EC2_PUBLIC_IP>:5080/training`

```

```