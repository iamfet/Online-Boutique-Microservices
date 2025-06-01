# Online-Boutique-Microservices

This repository contains a microservices-based reference application inspired by an online boutique shop. It demonstrates best practices for cloud-native development, infrastructure as code, CI/CD, and GitOps with ArgoCD.

This project is based on [GoogleCloudPlatform/microservices-demo](https://github.com/GoogleCloudPlatform/microservices-demo),licensed under Apache 2.0.


## Repository Structure

```
.
├── src/                  # Application source code for each microservice
├── infra/                # Infrastructure as Code (Terraform modules, etc.)
│   └── modules/
├── kubernetes/           # Kubernetes deployment manifests
├── .github/workflows/    # GitHub Actions CI/CD workflows
├── README.md             # Project documentation
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
- [Terraform](https://www.terraform.io/)
- [AWS CLI](https://aws.amazon.com/cli/)
- [Go](https://golang.org/) (for Go-based services)
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

### Build and Run Services

Each microservice has its own directory under `src/`. Example for the product catalog service:

```bash
cd src/productcatalogservice
docker build -t your-dockerhub-username/productcatalogservice:latest .
docker run -p 8080:8080 your-dockerhub-username/productcatalogservice:latest
```

### Deploy to Kubernetes

Apply manifests from the `kubernetes/` directory:

```bash
kubectl apply -f kubernetes/
```

### Continuous Deployment with ArgoCD

1. **Install ArgoCD** in your Kubernetes cluster (see [official docs](https://argo-cd.readthedocs.io/en/stable/getting_started/)).
2. **Register this repository** as a Git source in ArgoCD.
3. **Create an ArgoCD Application** pointing to the `kubernetes/` directory in this repo.
4. ArgoCD will automatically sync and deploy changes from Git to your cluster, enabling GitOps workflows.

### CI/CD

GitHub Actions workflows are defined in `.github/workflows/` for:

- Building and testing services
- Linting code
- Building and pushing Docker images
- Updating Kubernetes manifests

## Contributing

1. Fork the repository
2. Create a feature branch
3. Commit your changes
4. Open a pull request

## License

This project is licensed under the MIT License.

---

**Note:** Update secrets and configuration files as needed for your environment.
