# NutriFlow – DevSecOps CI/CD Pipeline and Kubernetes Deployment

## 1. Project Overview

**NutriFlow** is a full-stack application deployed using a DevSecOps approach. This project demonstrates how application development, security testing, containerization, continuous integration, continuous delivery, cloud deployment, and monitoring can be integrated into a single workflow.

The project uses Jenkins to automate the CI/CD pipeline, SonarCloud for code quality analysis, Docker for containerization, Amazon ECR for image storage, and Amazon EKS for Kubernetes-based deployment.

Security testing is performed using tools such as Semgrep, dependency auditing, OWASP ZAP, and Trivy. Argo CD is used for GitOps-based application deployment, while Prometheus and Grafana provide infrastructure and Kubernetes monitoring.

The main objective is to demonstrate a practical DevSecOps workflow by integrating security into different stages of the software delivery lifecycle.

## 2. Project Objectives

- Implement an automated CI/CD pipeline using Jenkins.
- Perform static code analysis and code quality checks.
- Integrate security testing into the development workflow.
- Identify vulnerable dependencies.
- Build and containerize frontend and backend applications using Docker.
- Push Docker images to Amazon Elastic Container Registry (ECR).
- Deploy containerized applications on Amazon EKS.
- Implement GitOps-based deployment using Argo CD.
- Configure Kubernetes Ingress for application access.
- Monitor Kubernetes resources using Prometheus and Grafana.
- Understand cloud infrastructure deployment and management using AWS.

## 3. Technology Stack

| Category | Technologies |
|---|---|
| Frontend | React, JavaScript, HTML, CSS |
| Backend | Python, FastAPI |
| Version Control | Git, GitHub |
| CI/CD | Jenkins |
| Code Quality | SonarCloud |
| SAST | Semgrep |
| Dependency Security | npm audit |
| DAST | OWASP ZAP |
| Container Security | Trivy |
| Containerization | Docker |
| Container Registry | Amazon ECR |
| Cloud Platform | AWS |
| Container Orchestration | Kubernetes |
| Managed Kubernetes | Amazon EKS |
| GitOps | Argo CD |
| Package Management | Helm |
| Ingress | NGINX Ingress Controller |
| Monitoring | Prometheus, Grafana |
| Infrastructure | AWS EC2, EKS, ECR, IAM |

## 4. Architecture

The project follows this DevSecOps workflow:

```text
                  Developer
                      |
                      v
                  GitHub
                      |
                      v
                   Jenkins
                      |
          +-----------+-----------+
          |                       |
          v                       v
   Frontend Build           Backend Build
          |                       |
          +-----------+-----------+
                      |
                      v
                SonarCloud
             Code Quality Analysis
                      |
                      v
               Docker Build
                      |
                      v
                 Amazon ECR
             Image Repository
                      |
                      v
                  Amazon EKS
                 Kubernetes
                      |
          +-----------+-----------+
          |                       |
          v                       v
     Backend Pod              Frontend Pod
          |                       |
          +-----------+-----------+
                      |
                      v
              NGINX Ingress
                      |
                      v
               NutriFlow App

               GitOps Workflow
                      |
                      v
                  Argo CD
                      |
                      v
              Kubernetes Manifests

               Monitoring
                      |
          +-----------+-----------+
          |                       |
          v                       v
      Prometheus               Grafana
          |                       |
          +-----------+-----------+
                      |
                      v
             Monitoring Dashboards
```

## 5. CI/CD Pipeline Using Jenkins

Jenkins automates the application build and Docker image publishing process.

### Pipeline Stages

**1. Checkout SCM**

- Retrieves the source code from the GitHub repository.
- Uses the configured Git repository and branch.

**2. Frontend Dependencies**

- Installs frontend dependencies.
- Prepares the frontend application for building.

**3. Frontend Lint**

- Runs frontend linting checks.
- Identifies potential coding issues and style violations.

**4. Frontend Build**

- Builds the frontend application.
- Checks whether the application can be successfully compiled.

**5. SonarCloud Analysis**

- Performs static code analysis.
- Identifies code quality issues and security-related findings.
- Evaluates the project against the configured quality gate.

**6. Backend Docker Build**

- Builds the backend Docker image.
- Packages the backend application and its dependencies.

**7. Frontend Docker Build**

- Builds the frontend Docker image.

**8. ECR Login**

- Authenticates Docker with Amazon Elastic Container Registry.
- Uses AWS credentials configured for the Jenkins environment.

**9. Push Images to ECR**

- Tags the frontend and backend Docker images.
- Pushes the images to their respective ECR repositories.

### Pipeline Workflow

```text
GitHub
   |
   v
Jenkins Checkout
   |
   v
Install Frontend Dependencies
   |
   v
Frontend Lint
   |
   v
Frontend Build
   |
   v
SonarCloud Analysis
   |
   v
Backend Docker Build
   |
   v
Frontend Docker Build
   |
   v
AWS ECR Authentication
   |
   v
Push Images to ECR
```

## 6. Security Testing

Security testing was performed using multiple tools to demonstrate different types of application security testing.

### 6.1 Static Application Security Testing (SAST) – Semgrep

Semgrep was used to analyze application source code without executing the application.

Purpose:

- Identify potentially insecure coding patterns.
- Detect common security weaknesses.
- Analyze backend and frontend source code.
- Identify areas requiring security improvements.

### 6.2 Dependency Security Scanning

Dependency auditing was performed to identify known vulnerabilities in application dependencies.

Tools and techniques:

- npm audit
- Dependency vulnerability analysis

Purpose:

- Identify vulnerable third-party packages.
- Review package security advisories.
- Identify dependencies that may require updates.

### 6.3 Dynamic Application Security Testing (DAST) – OWASP ZAP

OWASP ZAP was used to perform dynamic security testing against the running application.

Purpose:

- Analyze the application while it is running.
- Identify potential web application vulnerabilities.
- Inspect HTTP requests and responses.
- Generate security scan reports.

### 6.4 Container Security Scanning – Trivy

Trivy was used to scan container images for known vulnerabilities.

The scan was performed manually against a sample container image.

Example findings from the recorded scan:

| Severity | Vulnerabilities |
|---|---:|
| Critical | 4 |
| High | 13 |
| Medium | 16 |
| Low | 4 |
| **Total** | **37** |

These findings were from the scanned `python:3.13-alpine` image, not necessarily the NutriFlow application images.

Trivy scanning was performed as a separate manual security exercise rather than an automated Jenkins pipeline stage.

## 7. Docker Containerization

Docker was used to package the frontend and backend applications into separate container images.

Benefits:

- Consistent application environments.
- Easier deployment.
- Application isolation.
- Portability across environments.
- Simplified Kubernetes deployment.

Separate Docker images were created for:

- NutriFlow Backend
- NutriFlow Frontend

## 8. Amazon Elastic Container Registry (ECR)

Amazon ECR was used as the private Docker image registry.

Repositories:

```text
nutriflow-backend
nutriflow-frontend
```

The Jenkins pipeline authenticated with AWS and pushed the built Docker images to these repositories.

Example image naming format:

```text
104360117424.dkr.ecr.ap-south-1.amazonaws.com/nutriflow-backend:latest

104360117424.dkr.ecr.ap-south-1.amazonaws.com/nutriflow-frontend:latest
```

The images were stored in the Mumbai AWS region (`ap-south-1`).

## 9. Kubernetes Deployment Using Amazon EKS

Amazon Elastic Kubernetes Service (EKS) was used to deploy and manage the containerized NutriFlow application.

The Kubernetes deployment included:

- Backend Deployment
- Frontend Deployment
- Backend Service
- Frontend Service
- ConfigMap
- Namespace
- Ingress

### Kubernetes Components

| Component | Purpose |
|---|---|
| Namespace | Organizes NutriFlow resources |
| Deployment | Manages application Pods |
| Pod | Runs application containers |
| Service | Provides network access to Pods |
| ConfigMap | Stores application configuration |
| Ingress | Manages external HTTP access |

### Kubernetes Application Structure

```text
k8s/
│
├── namespace.yaml
├── configmap.yaml
├── backend-deployment.yaml
├── backend-service.yaml
├── frontend-deployment.yaml
├── frontend-service.yaml
├── ingress.yaml
└── secret.example.txt
```

### Application Deployment

The frontend and backend applications were deployed as separate Kubernetes workloads.

The backend API was tested successfully:

```bash
curl http://localhost:8002/nutriflow
```

Expected response:

```json
{
  "message": "Welcome to Nutriflow API"
}
```

## 10. GitOps Using Argo CD

Argo CD was installed in the Kubernetes cluster to implement GitOps-based deployment.

The Kubernetes manifests were maintained in GitHub, and Argo CD monitored the repository for changes.

### GitOps Workflow

```text
             GitHub Repository
                     |
                     v
                  Argo CD
                     |
                     v
             Kubernetes Manifests
                     |
                     v
                Amazon EKS
                     |
          +----------+----------+
          |                     |
          v                     v
      Backend                Frontend
       Pods                    Pods
```

### Argo CD Features Used

- Connected the GitHub repository.
- Configured the Kubernetes manifest directory.
- Created the NutriFlow application.
- Enabled automated synchronization.
- Monitored application synchronization and health status.

This demonstrated the GitOps approach, where Git acts as the source of truth for Kubernetes deployment configuration.

## 11. Application Exposure Using NGINX Ingress

NGINX Ingress Controller was deployed to provide external access to the application.

The Ingress resource was configured to route incoming HTTP traffic to the appropriate Kubernetes service.

Workflow:

```text
             External User
                   |
                   v
              AWS Load Balancer
                   |
                   v
             NGINX Ingress
                   |
          +--------+--------+
          |                 |
          v                 v
      Frontend           Backend
       Service            Service
          |                 |
          v                 v
      Frontend           Backend
        Pods               Pods
```

The application was accessed through the Ingress endpoint during deployment testing.

## 12. Monitoring Using Prometheus and Grafana

Prometheus and Grafana were deployed using the Prometheus Community `kube-prometheus-stack` Helm chart.

### Prometheus

Prometheus was used to collect and store monitoring metrics from the Kubernetes environment.

Monitoring components included:

- Kubernetes nodes
- Kubernetes Pods
- CPU utilization
- Memory utilization
- Kubernetes networking
- Kubelet metrics
- Node Exporter
- kube-state-metrics

### Grafana

Grafana was used to visualize the metrics collected by Prometheus.

The following dashboards were explored:

- Kubernetes / Compute Resources / Node (Pods)
- Kubernetes / Compute Resources / Pod
- Kubernetes / Networking / Cluster
- Kubernetes / Networking / Pod
- Kubernetes / Kubelet

Monitoring helped demonstrate how administrators can observe resource utilization and application infrastructure health.

### Monitoring Architecture

```text
              Kubernetes Cluster
                      |
          +-----------+-----------+
          |                       |
          v                       v
     Node Exporter          kube-state-metrics
          |                       |
          +-----------+-----------+
                      |
                      v
                  Prometheus
                      |
                      v
                   Grafana
                      |
                      v
              Monitoring Dashboards
```

## 13. AWS Infrastructure

The project was implemented using AWS services in the Mumbai region.

| AWS Service | Usage |
|---|---|
| Amazon EC2 | Hosted Jenkins and supporting tools |
| Amazon EKS | Managed Kubernetes environment |
| Amazon ECR | Stored Docker images |
| AWS IAM | Identity and access management |
| Elastic Load Balancing | Exposed the application through Ingress |
| Amazon VPC | Provided network infrastructure |

Region:

```text
ap-south-1 (Mumbai)
```

## 14. Repository Structure

The repository contains the application source code, CI/CD configuration, Docker-related files, and Kubernetes deployment manifests.

```text
NutriFlow-DevSecOps/
│
├── backend/
│   ├── Application source code
│   └── Dockerfile
│
├── frontend/
│   ├── Application source code
│   └── Dockerfile
│
├── k8s/
│   ├── namespace.yaml
│   ├── configmap.yaml
│   ├── backend-deployment.yaml
│   ├── backend-service.yaml
│   ├── frontend-deployment.yaml
│   ├── frontend-service.yaml
│   ├── ingress.yaml
│   └── secret.example.txt
│
├── Jenkinsfile
│
└── README.md
```

*Note: This is a representative project structure. The actual repository may contain additional application files and configuration.*

## 15. Deployment and Testing Results

The following activities were successfully demonstrated during the project:

- GitHub source code checkout through Jenkins.
- Frontend dependency installation and build.
- Frontend lint execution.
- SonarCloud code analysis.
- Backend and frontend Docker image builds.
- Docker image publishing to Amazon ECR.
- Kubernetes deployment on Amazon EKS.
- Backend and frontend Pod deployment.
- Application access through NGINX Ingress.
- Argo CD application synchronization.
- Prometheus metric collection.
- Grafana dashboard visualization.
- Manual security testing using Semgrep, dependency auditing, OWASP ZAP, and Trivy.

**Important:** SonarCloud reported code quality and security findings during analysis. The project demonstrated security scanning and reporting; it should not be considered completely vulnerability-free. Frontend linting also reported issues that require further improvement.

## 16. Current Infrastructure Status

The EKS environment was created and used for deployment, GitOps, and monitoring demonstrations.

After completing the deployment and capturing project evidence, the EKS cluster `nutriflow-cluster` was deleted to avoid unnecessary ongoing AWS infrastructure costs.

The Kubernetes deployment, Argo CD environment, and Prometheus/Grafana monitoring environment are therefore not currently running.

The following project assets remain available:

- GitHub source code.
- Jenkins pipeline configuration.
- Kubernetes YAML manifests.
- Dockerfiles.
- Amazon ECR repositories and images, unless separately deleted.
- Screenshots and reports captured during implementation.

The Jenkins EC2 instance is separate from the deleted EKS cluster.

## 17. Future Enhancements

Potential improvements include:

- Integrating Trivy image scanning into Jenkins.
- Automating Semgrep security scans in the CI/CD pipeline.
- Adding automated dependency vulnerability checks.
- Improving SonarCloud quality gate results.
- Fixing frontend linting issues.
- Implementing automated security report publishing.
- Configuring Prometheus alerting rules.
- Integrating Alertmanager with email or notification services.
- Adding application-specific monitoring metrics.
- Implementing centralized logging using an appropriate logging stack.
- Managing AWS infrastructure using Terraform.
- Improving IAM permissions using least-privilege policies.
- Implementing automated deployment rollback strategies.

## 18. Key Learnings

Through this project, I gained practical experience with:

- Building CI/CD pipelines using Jenkins.
- Integrating SonarCloud into application workflows.
- Performing SAST, DAST, dependency, and container security testing.
- Building and managing Docker images.
- Working with Amazon ECR.
- Deploying applications on Amazon EKS.
- Writing Kubernetes deployment and service manifests.
- Understanding Kubernetes networking and Ingress.
- Implementing GitOps using Argo CD.
- Installing Kubernetes tools using Helm.
- Monitoring Kubernetes resources using Prometheus and Grafana.
- Working with AWS infrastructure and cloud resource management.

## 19. Conclusion

NutriFlow demonstrates an end-to-end DevSecOps implementation by combining CI/CD automation, security testing, Docker containerization, AWS cloud services, Kubernetes orchestration, GitOps, and monitoring.

The project provided hands-on experience with tools and technologies commonly used in modern DevOps and DevSecOps environments, while demonstrating how application delivery and infrastructure monitoring can be integrated into a practical workflow.

---

**Project:** NutriFlow – DevSecOps CI/CD and Kubernetes Deployment  
**Cloud Platform:** AWS  
**Region:** ap-south-1 (Mumbai)  
**Container Registry:** Amazon ECR  
**CI/CD:** Jenkins  
**Orchestration:** Kubernetes / Amazon EKS  
**GitOps:** Argo CD  
**Monitoring:** Prometheus and Grafana
