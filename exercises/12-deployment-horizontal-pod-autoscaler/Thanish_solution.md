Step 1:

Create a Deployment named nginx with 1 replica. The Pod template of the Deployment should use container image nginx:1.23.4, set the CPU resource request to 0.5, and the memory resource request/limit to 500Mi.

YAML :

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx 
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx 
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.23.4
        resources:
          requests:
            cpu: "500m"
            memory: "500Mi"
          limits:
            memory: "500Mi"
        ports:
        - containerPort: 80
```

Step 2  :


Create a HorizontalPodAutoscaler for the Deployment named nginx-hpa that scales to a minimum of 3 and a maximum of 8 replicas. Scaling should happen based on an average CPU utilization of 75%, and an average memory utilization of 60%.

YAML :

```

apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: nginx-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: nginx  
  minReplicas: 3
  maxReplicas: 8
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 75
  - type: Resource
    resource:
      name: memory
      target:
        type: Utilization
        averageUtilization: 60
```

What this means is, 

```

If 3 pods consumes . 80 , 80 , 80 percent of cpu then 80+80+80 = 240 , 240/75 = 3.2 . So, replica will increase to 4. so it will be less than 75% avg .

If 3 pods consumes . 150,140,160 percent of cpu then 150+140+160 = 450/75 = 6 . So, replica will increase to 6 . so it will be less than 75% avg .

```
Validation :
```
C:\Users\thani\Kubernetes>kubectl get hpa
NAME        REFERENCE          TARGETS                       MINPODS   MAXPODS   REPLICAS   AGE
nginx-hpa   Deployment/nginx   cpu: 0%/75%, memory: 0%/60%   3         8         3          40m
```

As, the cpu is within the limits, the pods will be 3 only

