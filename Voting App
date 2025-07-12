# Saeed Sobhey 
#Vote App  task
#Namespace Setup
apiVersion: v1
kind: Namespace
metadata:
  name: vote
---
apiVersion: v1
kind: ResourceQuota
metadata:
  name: vote-quota
  namespace: vote
spec:
  hard:
    requests.cpu: "2"
    requests.memory: 2Gi
    limits.cpu: "4"
    limits.memory: 4Gi
    pods: "15"

# Redis Deployment
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis
  namespace: vote
spec:
  replicas: 1
  selector:
    matchLabels:
      app: redis
  template:
    metadata:
      labels:
        app: redis
    spec:
      containers:
      - name: redis
        image: redis
        ports:
        - containerPort: 6379
        resources:
          requests:
            cpu: "200m"
            memory: "128Mi"
          limits:
            cpu: "500m"
            memory: "256Mi"
---
apiVersion: v1
kind: Service
metadata:
  name: redis-service
  namespace: vote
spec:
  selector:
    app: redis
  ports:
  - port: 6379
    targetPort: 6379
  type: ClusterIP

# PostgreSQL Database
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: postgres
  namespace: vote
spec:
  replicas: 1
  selector:
    matchLabels:
      app: postgres
  template:
    metadata:
      labels:
        app: postgres
    spec:
      containers:
      - name: postgres
        image: postgres
        ports:
        - containerPort: 5432
        env:
        - name: POSTGRES_USER
          value: myuser
        - name: POSTGRES_PASSWORD
          value: mysecretpassword
        - name: POSTGRES_DB
          value: mydb
        resources:
          requests:
            cpu: "500m"
            memory: "256Mi"
          limits:
            cpu: "1"
            memory: "512Mi"
---
apiVersion: v1
kind: Service
metadata:
  name: db-service
  namespace: vote
spec:
  selector:
    app: postgres
  ports:
  - port: 5432
    targetPort: 5432
  type: ClusterIP

# Voting App
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: vote
  namespace: vote
spec:
  replicas: 2
  selector:
    matchLabels:
      app: vote
  template:
    metadata:
      labels:
        app: vote
    spec:
      containers:
      - name: vote
        image: docker/example-voting-app-vote
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: "100m"
            memory: "64Mi"
          limits:
            cpu: "300m"
            memory: "128Mi"
---
apiVersion: v1
kind: Service
metadata:
  name: vote-service
  namespace: vote
spec:
  selector:
    app: vote
  type: NodePort
  ports:
  - port: 80
    targetPort: 80
    nodePort: 31000

# Result App
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: result
  namespace: vote
spec:
  replicas: 2
  selector:
    matchLabels:
      app: result
  template:
    metadata:
      labels:
        app: result
    spec:
      containers:
      - name: result
        image: docker/example-voting-app-result
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: "100m"
            memory: "64Mi"
          limits:
            cpu: "300m"
            memory: "128Mi"
---
apiVersion: v1
kind: Service
metadata:
  name: result-service
  namespace: vote
spec:
  selector:
    app: result
  type: NodePort
  ports:
  - port: 80
    targetPort: 80
    nodePort: 31001

# Worker
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: worker
  namespace: vote
spec:
  replicas: 1
  selector:
    matchLabels:
      app: worker
  template:
    metadata:
      labels:
        app: worker
    spec:
      containers:
      - name: worker
        image: docker/example-voting-app-worker
        env:
        - name: REDIS_HOST
          value: redis-service
        - name: POSTGRES_HOST
          value: db-service
        - name: POSTGRES_PASSWORD
          value: mysecretpassword
        - name: POSTGRES_USER
          value: myuser
        - name: POSTGRES_DB
          value: mydb
        resources:
          requests:
            cpu: "200m"
            memory: "128Mi"
          limits:
            cpu: "500m"
            memory: "256Mi"
