# MEAN Stack Kubernetes Deployment

This repository contains Kubernetes configuration files and instructions for deploying a MEAN (MongoDB, Express.js, Angular, Node.js) stack application in a Kubernetes cluster.

## Table of Contents
- [Prerequisites](#prerequisites)
- [Project Structure](#project-structure)
- [Configuration Files](#configuration-files)
- [Deployment Steps](#deployment-steps)
- [Monitoring and Maintenance](#monitoring-and-maintenance)
- [Troubleshooting](#troubleshooting)

## Prerequisites

Before you begin, ensure you have the following installed:
- Docker (20.10.x or later)
- Kubernetes cluster (1.20.x or later)
- kubectl CLI tool
- A container registry account (Docker Hub, GCR, etc.)
- Node.js (16.x or later)
- Angular CLI (latest version)

## Project Structure

```
├── frontend/
│   ├── Dockerfile
│   ├── nginx.conf
│   └── src/
├── backend/
│   ├── Dockerfile
│   └── src/
├── k8s/
│   ├── frontend.yaml
│   ├── backend.yaml
│   ├── mongodb.yaml
│   └── secrets.yaml
└── README.md
```

## Configuration Files

### Frontend Dockerfile
```dockerfile
FROM node:16 as builder
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build --prod

FROM nginx:alpine
COPY --from=builder /app/dist/frontend /usr/share/nginx/html
COPY nginx.conf /etc/nginx/conf.d/default.conf
EXPOSE 80
```

### Backend Dockerfile
```dockerfile
FROM node:16
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["npm", "start"]
```

## Deployment Steps

1. **Build and Push Docker Images**
```bash
# Frontend
cd frontend
docker build -t your-registry/angular-frontend:latest .
docker push your-registry/angular-frontend:latest

# Backend
cd ../backend
docker build -t your-registry/express-backend:latest .
docker push your-registry/express-backend:latest
```

2. **Create MongoDB Secret**
```bash
# Create base64 encoded MongoDB URI
echo -n "mongodb://mongodb:27017/yourdb" | base64

# Create secret
kubectl apply -f k8s/secrets.yaml
```

3. **Deploy MongoDB**
```bash
kubectl apply -f k8s/mongodb.yaml
```

4. **Deploy Backend**
```bash
kubectl apply -f k8s/backend.yaml
```

5. **Deploy Frontend**
```bash
kubectl apply -f k8s/frontend.yaml
```

## Monitoring and Maintenance

### Check Deployment Status
```bash
# View all resources
kubectl get all

# Check pods
kubectl get pods

# Check services
kubectl get svc

# View logs
kubectl logs <pod-name>
```

### Scaling
```bash
# Scale frontend
kubectl scale deployment angular-frontend --replicas=3

# Scale backend
kubectl scale deployment express-backend --replicas=3
```

### Update Deployments
```bash
# Update images
kubectl set image deployment/angular-frontend angular-frontend=your-registry/angular-frontend:new-tag
kubectl set image deployment/express-backend express-backend=your-registry/express-backend:new-tag
```

## Troubleshooting

### Common Issues and Solutions

1. **Pods Not Starting**
   - Check pod status: `kubectl describe pod <pod-name>`
   - Check logs: `kubectl logs <pod-name>`
   - Verify resource limits

2. **Service Communication Issues**
   - Verify service names and ports
   - Check network policies
   - Ensure services are in the same namespace

3. **MongoDB Connection Issues**
   - Verify MongoDB secret is correctly created
   - Check MongoDB service is running
   - Validate connection string format

### Useful Debug Commands
```bash
# Get detailed pod information
kubectl describe pod <pod-name>

# Port forward to test services locally
kubectl port-forward service/angular-frontend 8080:80

# View container logs
kubectl logs -f <pod-name>

# Execute commands in containers
kubectl exec -it <pod-name> -- /bin/bash
```

## Environmental Considerations

### Development
- Use Minikube or Docker Desktop Kubernetes
- Enable local volume persistence
- Use NodePort or LoadBalancer services

### Production
- Configure proper resource limits
- Implement horizontal pod autoscaling
- Use ingress controllers
- Set up monitoring and logging
- Configure backups for MongoDB
- Use production-ready storage classes

## Security Best Practices

1. **Image Security**
   - Use specific image tags instead of 'latest'
   - Implement image scanning
   - Use private container registries

2. **Kubernetes Security**
   - Use RBAC policies
   - Implement network policies
   - Regular security updates
   - Secure secrets management

3. **Application Security**
   - Enable CORS properly
   - Implement rate limiting
   - Use TLS for all communications
   - Regular dependency updates

## Contributing

Please read CONTRIBUTING.md for details on our code of conduct and the process for submitting pull requests.

## License

This project is licensed under the MIT License - see the LICENSE file for details.
