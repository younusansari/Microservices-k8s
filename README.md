# Blue-Green Deployment Project

## Prerequisites
- Docker Desktop
- Minikube
- kubectl
- Helm
- Node.js
- Git

## Project Setup

### 1. Clone the Repository
```bash
git clone <your-repository-url>
cd blue-green-project
```

### 2. Local Development

#### Backend Setup
1. Navigate to backend directory
2. Install dependencies
```bash
cd backend
npm install
```
3. Create `.env` file with:
```
PORT=5000
MONGO_URI=your-mongodb-connection-string
```
4. Start backend server
```bash
npm start
```

#### Frontend Setup
1. Setup Blue Frontend
```bash
cd frontend-blue
npm install
```
2. Create `.env` file:
```
PORT=3100
```
3. Start blue frontend
```bash
npm start
```

3. Repeat similar steps for Green Frontend (with PORT=3200)

### 3. Dockerization

#### Build Docker Images
```bash
# Build Backend Image
docker build -t your-username/backend:v1 ./backend

# Build Blue Frontend Image
docker build -t your-username/frontend-blue:v1 ./frontend-blue

# Build Green Frontend Image
docker build -t your-username/frontend-green:v1 ./frontend-green
```

### 4. Kubernetes Deployment

#### Minikube Setup
1. Start Minikube
```bash
minikube start
```

2. Enable Required Addons
```bash
minikube addons enable metrics-server
minikube addons enable ingress
```

### 5. Create Kubernetes Manifest Files

#### Required Manifest Files
Create following files in `k8s/` directory:
- `backend-deployment.yaml`
- `frontend-blue-deployment.yaml`
- `frontend-green-deployment.yaml`
- `frontend-service.yaml`
- `ingress.yaml`

#### Service File Key Concepts
Your `frontend-service.yaml` should:
- Use selector to route traffic
- Define version (blue/green)
- Map ports correctly

### 6. Deploy to Minikube
```bash
# Apply all manifests
kubectl apply -f k8s/

# Verify deployments
kubectl get deployments
kubectl get services
kubectl get pods
```

### 7. Blue-Green Switching

#### Switch Traffic Methods

1. Basic Patch Command
```bash
# Switch to Green
kubectl patch service frontend-service -p '{"spec":{"selector":{"version":"green"}}}'

# Switch back to Blue
kubectl patch service frontend-service -p '{"spec":{"selector":{"version":"blue"}}}'
```

2. Detailed Patch Command
```bash
kubectl patch service frontend-service --type='merge' -p '{
  "spec":{
    "selector":{
      "app":"frontend",
      "version":"green"
    }
  }
}'
```

### 8. Verification
- Check service endpoints
- Verify traffic routing
- Monitor application logs

### Troubleshooting
- `kubectl get pods` - Check pod status
- `kubectl logs <pod-name>` - View logs
- `kubectl describe service frontend-service` - Service details

### Cleanup
```bash
# Remove deployments
kubectl delete -f k8s/

# Stop Minikube
minikube stop
```

## Blue-Green Deployment Flow Chart

```mermaid
graph TD
    A[Blue Environment Running] -->|Deploy Green| B[Green Environment Prepared]
    B -->|Validate Green| C{Green Ready?}
    C -->|Yes| D[Update Service Selector]
    C -->|No| B
    D -->|Redirect Traffic| E[Green Now Active]
    E -->|Rollback Option| A
```

### Flow Explanation
1. Blue environment is initial production
2. Green environment deployed alongside
3. Validate green environment 
4. Update service selector
5. Redirect traffic to green
6. Blue remains as rollback option

## Best Practices
- Implement health checks
- Use resource limits
- Configure monitoring
- Validate before switching
- Maintain rollback strategy


## License
This project is licensed under the MIT License


Deployment Evidence:

Project Structure (Clean & Production-Friendly)

To build docker images using docker compose: 
```
docker-compose build
```
<img width="1061" height="108" alt="image" src="https://github.com/user-attachments/assets/e3fec380-8c51-4062-976f-3391b4b3f123" />


Apply kuberbetes files 
```
kubectl apply -f .\k8s\.
```
<img width="1292" height="209" alt="image" src="https://github.com/user-attachments/assets/6793733c-f933-4275-a3e6-bdac7a07cab1" />


To test the application from your local device use port-forward
Backend service: 
```
kubectl port-forward svc/backend-service 5000:5000
```
<img width="1596" height="117" alt="image" src="https://github.com/user-attachments/assets/183dbae8-609f-4186-b4e0-c78e5a4a83d5" />


Open the browser and type: 
```
http://localhost:5000/health
```
<img width="650" height="217" alt="image" src="https://github.com/user-attachments/assets/bd12c182-98ba-44c1-941d-3c888ea5337b" />


Frontend service:
```
kubectl port-forward svc/frontend-service 8080:80
```
<img width="1606" height="79" alt="image" src="https://github.com/user-attachments/assets/f69bbbac-8951-4a5d-8c53-59e793b8631b" />


To get the current version selector: 
```
kubectl describe service frontend-service
```
<img width="1255" height="527" alt="image" src="https://github.com/user-attachments/assets/26882bb7-20ac-4437-8403-0cdfa5e592a6" />


To check if frontend is running on Blue services: 
```
http://localhost:8080/health
```
<img width="950" height="234" alt="image" src="https://github.com/user-attachments/assets/08ac46fc-db9d-46ae-9adc-230c1bc8a9d1" />


Switch the services to green:
```
kubectl patch service frontend-service -p '{"spec":{"selector":{"version":"green"}}}'
```
<img width="1247" height="97" alt="image" src="https://github.com/user-attachments/assets/3291832c-e4b1-4f19-a1d6-125167189107" />

Note: we have to stop the port-forwarding for frontend and re do it, however inside the node it works fine
```
kubectl port-forward svc/frontend-service 8080:80
```
<img width="1586" height="252" alt="image" src="https://github.com/user-attachments/assets/83f35c6b-f792-493d-b283-d204e3a171b5" />

Now test the health of frontend it should show Frontend services are running from Green application
```
http://localhost:8080/health
```
<img width="864" height="229" alt="image" src="https://github.com/user-attachments/assets/1a24bdb7-ad2a-4ad5-b4fd-ab6c62abd263" />


To clean up 
<img width="1476" height="189" alt="image" src="https://github.com/user-attachments/assets/606a4c60-4664-49eb-a90e-7a3621ccca5c" />

<img width="1433" height="290" alt="image" src="https://github.com/user-attachments/assets/40e99d91-dad0-4183-8e05-f0dcbf6030fd" />



