# Kubernetes

## Kubctl

- **Get Contexts:** kubectl config get-contexts

- **Set Current Context:** kubectl config use-context CONTEXT

- **Set Current Namespace:** kubectl config set-context --current --namespace=NAMESPACE

- **Apply File:** kubectl apply -f FILE.yml

- **Apply Files Within Directory:** kubectl apply -f DIRECTORY

- **Restart Deployment:** kubectl rollout restart deploy DEPLOYMENT

- **Get All:** kubectl get all

- **Delete All In Namespace:** kubectl delete all --all -n NAMESPACE

- **Delete Namespace:** kubectl delete namespace NAMESPACE

- **Logs Deployment:** kubectl logs -f deploy/DEPLOYMENT

- **Logs Pod:** kubectl logs -p POD

- **Logs:** kubectl logs --all-containers=true --selector app=APP --since=1h --tail=-1 > C:\Log.txt

## Azure

- **Install Azure Kubernetes CLI:** az aks install-cli

- **Set Subscription:** az account set --subscription SUBSCRIPTION

- **Connect Azure Kubernetes Service:** az aks get-credentials --resource-group RESOURCE_GROUP --name CLUSTER_NAME --admin

- **Export Azure Kubernetes Service Configuration:** az aks get-credentials --resource-group RESOURCE_GROUP --name CLUSTER_NAME --admin --file FILE

- **Create Azure Credentials:** az ad sp create-for-rbac --name NAME --role contributor --scopes /subscriptions/SUBSCRIPTION/resourceGroups/RESOURCE_GROUP --sdk-auth

## ConfigMap

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  namespace: NAMESPACE
  name: NAME
  labels:
    app: NAME
data:
  ASPNETCORE_ENVIRONMENT: Production
  ASPNETCORE_FORWARDEDHEADERS_ENABLED: "true"
  Serilog__WriteTo__1__Args__path: /logs
```

## Deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  namespace: NAMESPACE
  name: NAME
  labels:
    app: NAME
spec:
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 50%
      maxUnavailable: 50%
  replicas: 1
  revisionHistoryLimit: 1
  selector:
    matchLabels:
      app: NAME
  template:
    metadata:
      labels:
        app: NAME
    spec:
      nodeSelector:
        app: NAME
      containers:
        - name: NAME
          image: REGISTRY/PROJECT/IMAGE
          envFrom:
            - configMapRef:
              name: NAME
          ports:
            - containerPort: 80
          resources:
            requests:
              cpu: 100m
              memory: 128Mi
            limits:
              cpu: 500m
              memory: 1Gi
          readinessProbe:
            initialDelaySeconds: 20
            periodSeconds: 60
            timeoutSeconds: 10
            tcpSocket:
              port: 80
            httpGet:
              path: /
              port: 80
          livenessProbe:
            initialDelaySeconds: 20
            periodSeconds: 60
            timeoutSeconds: 10
            tcpSocket:
              port: 80
            httpGet:
              path: /
              port: 80
```

## HorizontalPodAutoscaler

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  namespace: NAMESPACE
  name: NAME
  labels:
    app: NAME
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: NAME
  minReplicas: 2
  maxReplicas: 10
  metrics:
    - type: Resource
      resource:
        name: cpu
        target:
          type: Utilization
          averageUtilization: 90
    - type: Resource
      resource:
        name: memory
        target:
          type: Utilization
          averageUtilization: 90
```

## Ingress

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  namespace: NAMESPACE
  name: NAME
  labels:
    app: NAME
  annotations:
    kubernetes.io/ingress.class: nginx
spec:
  rules:
    - host: "*.HOST.COM"
      http:
        paths:
          - path: "/"
            pathType: Prefix
            backend:
              service:
                name: NAME
                port:
                  number: 80
```

## Namespace

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: NAME
  labels:
    app: NAME
```

## Secret

```yaml
apiVersion: v1
kind: Secret
metadata:
  namespace: NAMESPACE
  name: NAME
  labels:
    app: NAME
type: Opaque
data:
  Key: Value
```

## Service

```yaml
apiVersion: v1
kind: Service
metadata:
  namespace: NAMESPACE
  name: NAME
  labels:
    app: NAME
spec:
  type: ClusterIP
  ports:
    - port: 80
      targetPort: 80
  selector:
    app: NAME
```
