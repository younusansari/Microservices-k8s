# Microservices-Task

## Overview
This document provides details on testing various services after running the `docker-compose` file. These services include User, Product, Order, and Gateway Services. Each service has its own endpoints for testing purposes.

---

## Services and Endpoints

### **User Service**
- **Base URL:** `http://localhost:3000`
- **Endpoints:**
  - **List Users:**  
    ```
    curl http://localhost:3000/users
    ```
    Or open in your browser: [http://localhost:3000/users](http://localhost:3000/users)

---

### **Product Service**
- **Base URL:** `http://localhost:3001`
- **Endpoints:**
  - **List Products:**  
    ```
    curl http://localhost:3001/products
    ```
    Or open in your browser: [http://localhost:3001/products](http://localhost:3001/products)

---

### **Order Service**
- **Base URL:** `http://localhost:3002`
- **Endpoints:**
  - **List Orders:**  
    ```
    curl http://localhost:3002/orders
    ```
    Or open in your browser: [http://localhost:3002/orders](http://localhost:3002/orders)

---

### **Gateway Service**
- **Base URL:** `http://localhost:3003/api`
- **Endpoints:**
  - **Users:**  
    ```
    curl http://localhost:3003/api/users
    ```
  - **Products:**  
    ```
    curl http://localhost:3003/api/products
    ```
  - **Orders:**  
    ```
    curl http://localhost:3003/api/orders
    ```

---

## Instructions
1. Start all services using the `docker-compose` file:
   ```
   docker-compose up
   ```
2. Once the services are running, use the above endpoints to verify the functionality.

Happy testing!

## In order to deploy this application in Kubernetes follow the below instructions

1. Tag the lable and push your docker images to DockerHub or any other registry

   ```
   docker tag microservices_order-service:latest younusansari/order-service:latest
   docker tag microservices_product-service:latest younusansari/product-service:latest
   docker tag microservices_user-service:latest younusansari/user-service:latest
   docker tag microservices_gateway-service:latest younusansari/gateway-service:latest
   ```
2. Push Docker images to docker hub
   ```
   docker push younusansari/user-service:latest
   docker push younusansari/product-service:latest
   docker push younusansari/order-service:latest
   docker push younusansari/gateway-service:latest
   ```
3. Create Deployment yaml files

a. deployment-user.yaml
   ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: user-deployment
      labels:
        app: user
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: user
      template:
        metadata:
          labels:
            app: user
        spec:
          containers:
          - name: user
            image: younusansari/user-service:latest
            ports:
            - containerPort: 3000
            resources:
              limits:
                cpu: "1"
              requests:
                cpu: "0.5"
      ```
