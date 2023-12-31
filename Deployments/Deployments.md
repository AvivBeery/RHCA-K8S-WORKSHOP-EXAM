1. Create a deployment called webapp with image nginx with 5 replicas 
a. Use the below command to create a yaml file. 
i. kubectl create deploy webapp --image=nginx --dry-run -o yaml > 
webapp.yaml  
ii. Edit it and add 5 replica’s
```
vi webapp.yaml
```
```
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: webapp
  name: webapp
spec:
  replicas: 5
  selector:
    matchLabels:
      app: webapp
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: webapp
    spec:
      containers:
      - image: nginx
        name: nginx
        resources: {}
status: {}
```
```
kubectl create deploy webapp --image=nginx --dry-run -o yaml & webapp.yaml
```
2. Get the deployment rollout status 
```
kubectl rollout status deploy webapp
```
3. Get the replicaset that created with this deployment 
```
kubectl get rs -l app=webapp
```
NAME                DESIRED   CURRENT   READY   AGE
webapp-6684ccd7b8   5         5         5       3m18s

4. EXPORT the yaml of the replicaset and pods of this deployment
```
kubectl get rs -l app=webapp -o yaml
kubectl get po -l app=webapp -o yaml
```
5. Delete the deployment you just created and watch all the pods are also being 
deleted
```
kubectl delete deploy webapp
kubectl get po -l app=webapp -w
```
6. Create a deployment of webapp with image nginx:1.17.1 with container port 80 and 
verify the image version 
a. kubectl create deploy webapp --image=nginx:1.17.1 --dry-run -o yaml > 
webapp.yaml 
b. add the port section (80)  and create the deployment 
```
vi webapp.yaml
```
add the port section and create the deployment
```
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: webapp
  name: webapp
spec:
  replicas: 1
  selector:
    matchLabels:
      app: webapp
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: webapp
    spec:
      containers:
      - image: nginx:1.17.1
        name: nginx
        ports:
        - containerPort: 80
        resources: {}
status: {}
```
```
kubectl create deploy webapp --image=nginx:1.17.1 --dry-run -o yaml & webapp.yaml
```
```
kubectl create -f webapp.yaml
```
verify:
```
kubectl describe deploy webapp | grep Image
```
   Image:        nginx:1.17.1

7. Update the deployment with the image version 1.17.4 and verify 
```
kubectl set image deploy/webapp nginx=nginx:1.17.4
```
```
kubectl describe deploy webapp | grep Image
```
    Image:        nginx:1.17.4

8. Check the rollout history and make sure everything is ok after the update 
```
kubectl rollout history deploy webapp
```
deployment.apps/webapp
REVISION  CHANGE-CAUSE
1         <none>
2         <none>
```
kubectl get deploy webapp --show-labels
```
NAME     READY   UP-TO-DATE   AVAILABLE   AGE     LABELS
webapp   1/1     1            1           2m29s   app=webapp
```
kubectl get rs -l app=webapp
```
NAME                DESIRED   CURRENT   READY   AGE
webapp-684ff84dd7   0         0         0       2m44s
webapp-788f8c85b6   1         1         1       102s
```
kubectl get po -l app=webapp
```
NAME                      READY   STATUS    RESTARTS   AGE
webapp-788f8c85b6-qj5dm   1/1     Running   0          119s

9. Undo the deployment to the previous version 1.17.1 and verify Image has the 
previous version
```
kubectl rollout undo deploy webapp
```
deployment.apps/webapp rolled back
```
kubectl describe deploy webapp | grep Image
```
Image:        nginx:1.17.1

10. Update the deployment with the wrong image version 1.100 and verify something is 
wrong with the deployment
```
kubectl set image deploy/webapp nginx=nginx:1.100
```
```
kubectl rollout status deploy webapp (Waiting for deployment "webapp" rollout to finish: 1 old replicas are pending termination...)
```
a. Expect: kubectl get pods (ImagePullErr) 
kubectl get pods (webapp-77b685f94f-rqv85   0/1     ErrImagePull   0          59s)
b.  Undo the deployment with the previous version and verify everything is Ok 
```
kubectl rollout undo deploy webapp
```
```
kubectl rollout status deploy webapp 
```
(deployment "webapp" successfully rolled out)

```
kubectl get pods
```
(webapp-684ff84dd7-rtb9p   1/1     Running   0          16m)

c. kubectl rollout history deploy webapp --revision=7 
error: unable to find the specified revision
d. Check the history of the specific revision of that deployment 
```
kubectl rollout history deploy webapp
```
deployment.apps/webapp 
REVISION  CHANGE-CAUSE
2         <none>
4         <none>
5         <none>
e. update the deployment with the image version latest and check the history 
and verify nothing is going on
```
kubectl set image deploy/webapp nginx=nginx:latest
```
```
kubectl rollout history deploy webapp
```
deployment.apps/webapp 
REVISION  CHANGE-CAUSE
2         <none>
4         <none>
5         <none>
6         <none>


11. Apply the autoscaling to this deployment with minimum 10 and maximum 20 replicas 
and target CPU of 85% and verify hpa is created and replicas are increased to 10 
from 1
```
kubectl autoscale deploy webapp --min=10 --max=20 --cpu-percent=85
```
```
kubectl get hpa
```
NAME     REFERENCE           TARGETS         MINPODS   MAXPODS   REPLICAS   AGE
webapp   Deployment/webapp   <unknown>/85%   10        20        0          8s
kubectl get pod -l app=webapp
NAME                      READY   STATUS    RESTARTS   AGE
webapp-84bc478888-4pkk8   1/1     Running   0          7s
webapp-84bc478888-8jzvb   1/1     Running   0          7s
webapp-84bc478888-dt8gr   1/1     Running   0          7s
webapp-84bc478888-gbskw   1/1     Running   0          7s
webapp-84bc478888-hgxnk   1/1     Running   0          7s
webapp-84bc478888-hv564   1/1     Running   0          7s
webapp-84bc478888-jrf8z   1/1     Running   0          2m15s
webapp-84bc478888-r6xgv   1/1     Running   0          7s
webapp-84bc478888-t9ftv   1/1     Running   0          7s
webapp-84bc478888-xzfct   1/1     Running   0          7s


13. Clean the cluster by deleting deployment and hpa you just created 
```
kubectl delete deploy webapp
```
deployment.apps "webapp" deleted
```
kubectl delete hpa webapphorizontalpodautoscaler.autoscaling "webapp" deleted
```

14. Create a job and make it run 10 times one after one (run > exit > run >exit ..) using 
the following configuration: 
```
kubectl create job hello-job --image=busybox --dry-run -o yaml -- echo "Hello I am from job" > hello-job.yaml”
```
```
vi hello-job.yaml
```
```
apiVersion: batch/v1
kind: Job
metadata:
  creationTimestamp: null
  name: hello-job
spec:
  template:
    metadata:
      creationTimestamp: null
    spec:
      containers:
      - command:
        - echo
        - Hello I am from job
        image: busybox
        name: hello-job
        resources: {}
      restartPolicy: Never
status: {}
```
```
kubectl create job hello-job --image=busybox --dry-run -o yaml -- echo "Hello I am from job" & hello-job.yaml
```
```
kubectl create -f hello-job.yaml
```
job.batch/hello-job created

a. Add to the above job completions: 10 inside the yaml
```
apiVersion: batch/v1
kind: Job
metadata:
  creationTimestamp: null
  name: hello-job
spec:
  completions: 10
  template:
    metadata:
      creationTimestamp: null
    spec:
      containers:
      - command:
        - echo
        - Hello I am from job
        image: busybox
        name: hello-job
        resources: {}
      restartPolicy: Never
status: {}
```
