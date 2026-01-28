Step 1 :Create a deployement with name nginx and replica set : 3 and with other given requirnment .

    deployement.yaml
    
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: nginx
      labels:
        tier: backend
    spec:
      replicas: 3
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
            ports:
            - containerPort: 80

To verify : kubectl get deployement

Step 2: 

Update the image : 

    Command : kubectl edit deployement nginx

Step 3:

To increase the replica set : 

    kubectl scale deployment/nginx --replicas=5

Step 4: 

Check the history of deployements

    kubectl rollout history deployment/nginx

Step 5 :

    To revert to previous version : kubectl rollout undo deployment/nginx --to-revision=1
