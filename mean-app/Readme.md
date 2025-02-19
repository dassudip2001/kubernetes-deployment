# MEAN Stack Kubernetes Deployment

## Overview
This project deploys a MEAN (MongoDB, Express, Angular, Node.js) stack application on Kubernetes. It includes services and deployments for the frontend, backend, and MongoDB database.

## Prerequisites
- [Kubernetes](https://kubernetes.io/docs/setup/) installed
- [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) command-line tool
- Access to a Kubernetes cluster

## Getting Started

### Installation
1. Clone the repository:
   ```bash
   git clone https://github.com/yourusername/mean-kubernetes.git
   cd mean-kubernetes
   ```

2. Apply the Kubernetes configurations:
   ```bash
   kubectl apply -f mean-app/mean.yml
   ```

### Usage
- To check the status of your pods:
  ```bash
  kubectl get pods
  ```

- To access the Angular frontend:
  ```bash
  kubectl port-forward svc/angular-frontend 8080:80
  ```

- To access the Express backend:
  ```bash
  kubectl port-forward svc/express-backend 3000:3000
  ```

## Configuration
- Configuration files are located in the `mean-app/` directory.
- Modify the YAML files as needed for your environment, especially the image names and backend URL.

## Secrets Management
- MongoDB connection string is stored in a Kubernetes Secret named `mongodb-secret`. Ensure to update the `mongodb-uri` key with your actual connection string.

## Contributing
1. Fork the repository.
2. Create a new branch (`git checkout -b feature-branch`).
3. Make your changes and commit them (`git commit -m 'Add new feature'`).
4. Push to the branch (`git push origin feature-branch`).
5. Create a new Pull Request.

## License
This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Acknowledgments
- Thanks to the Kubernetes community for their support and resources.