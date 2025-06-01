# Online-Boutique-Microservices

This repository contains a microservices-based reference application inspired by an online boutique shop. It demonstrates best practices for cloud-native development, infrastructure as code, CI/CD, and GitOps with ArgoCD.

This project is based on [GoogleCloudPlatform/microservices-demo](https://github.com/GoogleCloudPlatform/microservices-demo), licensed under Apache 2.0.

## Repository Structure

```
.
├── src/                  # Application source code for each microservice
├── infra/                # Infrastructure as Code (Terraform modules, etc.)
│   └── modules/
├── kubernetes/           # Kubernetes deployment manifests
├── .github/workflows/    # GitHub Actions CI/CD workflows
├── README.md             # Project documentation
├── Install AWS Load Balancer Controller.md # Guide for installing AWS Load Balancer Controller
└── ...
```

## Features

- **Microservices Architecture:** Modular services for product catalog, cart, checkout, and more.
- **Infrastructure as Code:** Terraform modules for AWS resources (VPC, EKS, etc.).
- **Kubernetes Native:** Manifests for deploying services to Kubernetes.
- **CI/CD:** Automated build, test, lint, Docker image publishing using GitHub Actions.
- **GitOps with ArgoCD:** Continuous deployment and synchronization of Kubernetes manifests from Git.
- **Dockerized:** Each service can be built and run as a container.

## Getting Started

### Prerequisites

- [Docker](https://www.docker.com/)
- [kubectl](https://kubernetes.io/docs/tasks/tools/)
- [eksctl](https://eksctl.io/installation/)
- [Terraform](https://www.terraform.io/)
- [AWS CLI](https://aws.amazon.com/cli/)
- [Go](https://golang.org/) (for Go-based services)
- [Helm](https://helm.sh/) (for installing controllers)
- [ArgoCD](https://argo-cd.readthedocs.io/en/stable/) (for GitOps CD)

### Infrastructure Setup

1. **Configure AWS credentials** for Terraform.
2. **Create a `terraform.tfvars` file** in the `infra` directory to provide values for required Terraform variables (example below).
3. **Initialize and apply Terraform:**
   ```bash
   cd infra
   terraform init
   terraform apply
   ```

#### Example `terraform.tfvars`

```hcl
region             = "us-west-2"
vpc_cidr           = "10.0.0.0/16"
private_subnet_cidrs = ["10.0.1.0/24", "10.0.2.0/24"]
public_subnet_cidrs  = ["10.0.101.0/24", "10.0.102.0/24"]
cluster_name       = "online-boutique-eks"
cluster_version    = "1.29"
node_groups = {
  default = {
    instance_types = ["t3.medium"]
    capacity_type  = "ON_DEMAND"
    scaling_config = {
      desired_size = 2
      max_size     = 3
      min_size     = 1
    }
  }
}
```

Update the values as needed for your environment.

---

## AWS Load Balancer Controller

## AWS Load Balancer Controller

To enable Kubernetes Ingress resources with AWS Application Load Balancer, install the AWS Load Balancer Controller in your EKS cluster.  
See [Install AWS Load Balancer Controller.md](./Install%20AWS%20Load%20Balancer%20Controller.md) for step-by-step instructions in this repository.

---

## ArgoCD Installation

To enable GitOps continuous deployment, install ArgoCD in your Kubernetes cluster:

```bash
# Create the argocd namespace
kubectl create namespace argocd

# Install ArgoCD using the official manifests
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# (Optional) Expose the ArgoCD API server (for local access)
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

**Access the ArgoCD UI:**
- Open [https://localhost:8080](https://localhost:8080) in your browser.

**Get the initial admin password:**
```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo
```

**Login to ArgoCD CLI:**
```bash
argocd login localhost:8080
```

**Register your application:**
- Create an ArgoCD Application pointing to the `kubernetes/` directory in this repo.

---

## Build and Run Services

Each microservice has its own directory under `src/`. Example for the product catalog service:

```bash
cd src/productcatalogservice
docker build -t your-dockerhub-username/productcatalogservice:latest .
docker run -p 8080:8080 your-dockerhub-username/productcatalogservice:latest
```

---

## Deploy to Kubernetes

Apply manifests from the `kubernetes/` directory:

```bash
kubectl apply -f kubernetes/
```

---

## CI/CD

GitHub Actions workflows are defined in `.github/workflows/` for:
- Building and testing services
- Linting code
- Building and pushing Docker images
- Updating Kubernetes manifests

---

## Contributing

1. Fork the repository
2. Create a feature branch
3. Commit your changes
4. Open a pull request

## License

This project is licensed under the MIT License.

---

**Note:** Update secrets and configuration files as needed for your environment.
