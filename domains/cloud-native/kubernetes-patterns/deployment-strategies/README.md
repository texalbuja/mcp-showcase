# Kubernetes Deployment Strategies

This section demonstrates different deployment strategies for Kubernetes applications.

## Overview of Deployment Strategies

1. **Rolling Update** - Default strategy, gradually replaces instances
2. **Blue/Green Deployment** - Runs two identical environments, switches traffic
3. **Canary Deployment** - Routes a small percentage of traffic to new version
4. **A/B Testing** - Similar to canary but based on specific user segments
5. **Shadow Deployment** - Runs new version alongside old one with duplicated traffic

## Examples in this Directory

This directory contains example YAML manifests for each deployment strategy.

## Rolling Update Strategy

The rolling update strategy is the default deployment strategy in Kubernetes. It gradually replaces pods of the previous version with pods of the new version.

### rolling-update.yaml

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app-deployment
  labels:
    app: my-app
spec:
  replicas: 3
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1        # Maximum number of pods above desired number
      maxUnavailable: 1  # Maximum number of unavailable pods during update
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: app-container
        image: my-app:1.0.0
        ports:
        - containerPort: 8080
        readinessProbe:
          httpGet:
            path: /health
            port: 8080
          initialDelaySeconds: 5
          periodSeconds: 10
        resources:
          requests:
            memory: "64Mi"
            cpu: "100m"
          limits:
            memory: "128Mi"
            cpu: "200m"
```

## Blue/Green Deployment

Blue/Green deployment runs two identical environments with different versions. Traffic is switched from one to the other once the new version is verified.

### blue-deployment.yaml

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app-blue
  labels:
    app: my-app
    version: blue
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
      version: blue
  template:
    metadata:
      labels:
        app: my-app
        version: blue
    spec:
      containers:
      - name: app-container
        image: my-app:1.0.0
        ports:
        - containerPort: 8080
```

### green-deployment.yaml

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app-green
  labels:
    app: my-app
    version: green
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
      version: green
  template:
    metadata:
      labels:
        app: my-app
        version: green
    spec:
      containers:
      - name: app-container
        image: my-app:1.1.0
        ports:
        - containerPort: 8080
```

### service.yaml (initially pointing to blue)

```yaml
apiVersion: v1
kind: Service
metadata:
  name: app-service
spec:
  selector:
    app: my-app
    version: blue  # Switch to "green" to change traffic
  ports:
  - port: 80
    targetPort: 8080
  type: ClusterIP
```

## Canary Deployment

Canary deployment routes a small percentage of traffic to the new version for testing.

### canary-example.yaml

```yaml
# Original deployment (90% of traffic)
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app-stable
  labels:
    app: my-app
    version: stable
spec:
  replicas: 9  # 90% of pods
  selector:
    matchLabels:
      app: my-app
      version: stable
  template:
    metadata:
      labels:
        app: my-app
        version: stable
    spec:
      containers:
      - name: app-container
        image: my-app:1.0.0
        ports:
        - containerPort: 8080

---
# Canary deployment (10% of traffic)
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app-canary
  labels:
    app: my-app
    version: canary
spec:
  replicas: 1  # 10% of pods
  selector:
    matchLabels:
      app: my-app
      version: canary
  template:
    metadata:
      labels:
        app: my-app
        version: canary
    spec:
      containers:
      - name: app-container
        image: my-app:1.1.0
        ports:
        - containerPort: 8080

---
# Service (selects both deployments)
apiVersion: v1
kind: Service
metadata:
  name: app-service
spec:
  selector:
    app: my-app  # Selects both stable and canary pods
  ports:
  - port: 80
    targetPort: 8080
  type: ClusterIP
```

## Best Practices

1. Always implement health checks (readiness and liveness probes)
2. Set appropriate resource limits
3. Define update strategies explicitly
4. Use labels and selectors effectively
5. Consider stateful vs. stateless applications when choosing strategy
6. Implement monitoring and rollback mechanisms
7. Test strategies in non-production environments first
