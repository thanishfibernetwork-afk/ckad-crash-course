


Step 1 – Create the blue deployment

YAML :
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-blue
spec:
  replicas: 3
  selector:
    matchLabels:
      version: blue
  template:
    metadata:
      labels:
        version: blue
    spec:
      containers:
      - name: nginx
        image: nginx:1.23.0
        ports:
        - containerPort: 80
```
Step 2 – Expose the blue deployment using a ClusterIP Service

```
apiVersion: v1
kind: Service
metadata:
  name: nginx
spec:
  type: ClusterIP
  selector:
    version: blue
  ports:
  - port: 80
    targetPort: 80

Step 3 – Test access to the Service using a temporary curl Pod

apiVersion: v1
kind: Pod
metadata:
  name: curl-blue-test
spec:
  restartPolicy: Never
  containers:
  - name: curl
    image: alpine/curl:8.5.0
    command:
      - sh
      - -c
      - curl http://nginx
```
kubectl logs curl-blue-test
```
C:\Users\thani\Kubernetes>kubectl logs curl-blue-test
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
100   615  100   615    0     0  26639      0 --:--:-- --:--:-- --:--:-- 36176
```

Step 4 – Create the green deployment

yaml :
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-green
spec:
  replicas: 3
  selector:
    matchLabels:
      version: green
  template:
    metadata:
      labels:
        version: green
    spec:
      containers:
      - name: nginx
        image: nginx:1.23.4
        ports:
        - containerPort: 80
```
Step 5 – Switch traffic to the green deployment
```
apiVersion: v1
kind: Service
metadata:
  name: nginx
spec:
  type: ClusterIP
  selector:
    version: green
  ports:
  - port: 80
    targetPort: 80
```
Step 6 – Delete the blue deployment

kubectl delete deployment nginx-blue

Step 7 – Test the Service again using a temporary Pod
```
apiVersion: v1
kind: Pod
metadata:
  name: curl-green-test
spec:
  restartPolicy: Never
  containers:
  - name: curl
    image: alpine/curl:8.5.0
    command:
      - sh
      - -c
      - curl http://nginx
```
Validation : kubectl logs curl-green-test
```
C:\Users\thani\Kubernetes>kubectl logs curl-green-test
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   615  100   615    0     0  76473      0 --:--:-- --:--:-- --:--:--  300k
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
```
