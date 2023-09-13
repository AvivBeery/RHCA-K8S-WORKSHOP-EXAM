![image](https://github.com/AvivBeery/RHCA-K8S-WORKSHOP-EXAM/assets/142738883/ce2bd1e9-d9e3-4cca-b213-379cfc410d05)
1. Deploy a pod named nginx-pod using the nginx:alpine image
```
kubectl run nginx-pod-aviv --image=nginx:alpine
```
2. Deploy a messaging pod using the redis:alpine image with the labels set to tier=msg.
Pod Name: messaging
Image: redis:alpine
```
kubectl run messaging --image=redis:alpine --labels=tier=msg
```

3. Create a namespace named apx-x998-yourname
```
kubectl create ns apx-x998-aviv
```

4. Get the list of nodes in JSON format and store it in a file at /tmp/nodes-yourname
```
kubectl get nodes -o json > /tmp/nodes-aviv.json
```
5. Create a service messaging-service to expose the messaging application within the
cluster on port 6379.
a. Use imperative commands - kubectl
b. Service: messaging-service
c. Port: 6379
d. Type: ClusterIp
e. Use the right labels
```
kubectl expose pod messaging --name=messaging-service --port 6379
```
6.Create a service messaging-service to expose the messaging application within the
cluster on port 6379.
a. Service: messaging-service
b. Port: 6379
c. Type: ClusterIp
d. Use the right labels:
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: masseging
spec:
  selector:
    matchLabels:
      run: tier=msg
  replicas:
  template:
    metadata:
      labels:
        run: tier=msg
    spec:
      containers:
      - name: messaging
        image: redis:alpine
        ports:
        - containerPort: 6379
```
 7. Create a deployment named hr-web-app using the image kodekloud/webapp-color
with 2 replicas
a. Name: hr-web-app
b. Image: kodekloud/webapp-color
c. Replicas: 2
```
kubectl create deploy shr-web-app --image  kodekloud/webapp-color --replicas=2
```
8.Create a static pod named static-busybox on the master node that uses the busybox
image and the command sleep 1000
a. Name: static-busybox
b. Image: busybox
```
kubectl run static-busybox --image=busybox --dry-run=client -oyaml --command -- sleep 1000 & /etc/kubernetes/manifests/static-busybox.yaml
```
```
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: static-busybox
  name: static-busybox
spec:
  containers:
  - command:
    - sleep
    - "1000"
    image: busybox
    name: static-busybox
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
```
9. Create a POD in the finance-yourname namespace named temp-bus with the image
redis:alpine
a. Name: temp-bus
b. Image Name: redis:alpine  
```
kubectl run finane-aviv -n finance temp-bus --image redis:alpine
```
10. Create a Persistent Volume with the given specification
a. Volume Name: pv-analytics
b. Storage: 100Mi
c. Access modes: ReadWriteMany
d. Host Path: /pv/data-analytics
https://raw.githubusercontent.com/AvivBeery/RHCA-K8S-WORKSHOP-EXAM/main/10.%20create%20persistent%20volume
```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-analytics
  labels:
    type: local
spec:
  storageClassName: manual
  capacity:
    storage: 100Mi
  accessModes:
    - ReadWriteMany
  hostPath:
    path: /pv/data-analytics
```
11. Create a Pod called redis-storage-yourname with image: redis:alpine with a Volume
of type emptyDir that lasts for the life of the Pod. specs:.
a. Pod named 'redis-storage-yourname'
b. Pod 'redis-storage-yourname' uses Volume type of emptyDir
c. Pod 'redis-storage-yourname' uses volumeMount with mountPath =
/data/redis
https://raw.githubusercontent.com/AvivBeery/RHCA-K8S-WORKSHOP-EXAM/main/11.create%20pod%20redis-storage
```
apiVersion: v1
kind: Pod
metadata:
  name: redis-storage-aviv
spec:
  containers:
  - image: redis:alpine
    name: test-container
    volumeMounts:
    - mountPath: /data/redis
      name: redis-storage-aviv
  volumes:
  - name: redis-storage-aviv
    emptyDir: {}
```
12. Create this pod and attached it a persistent volume called pv-1
a. Make sure the PV mountPath is hostbase : /data
```
apiVersion: v1
kind: Pod
metadata:
 creationTimestamp: null
 labels:
 run: use-pv
 name: use-pvspec-aviv
 containers:
 - image: nginx
 name: use-pv
 resources: {}
 dnsPolicy: ClusterFirst
 restartPolicy: Always
status: {}
    volumeMounts:
    - mountPath: /data/redis
      name: use-pvspec-aviv
  volumes:
  - name: use-pvspec-aviv
```
13. Create a new deployment called nginx-deploy, with image nginx:1.16 and 1 replica.
Record the version. Next upgrade the deployment to version 1.17 using rolling
update. Make sure that the version upgrade is recorded in the resource annotation.
a. Deployment : nginx-deploy. Image: nginx:1.16
b. Image: nginx:1.16
c. Task: Upgrade the version of the deployment to 1:17
d. Task: Record the changes for the image upgrade
```
kubectl run nginx-deploy --image nginx:1.16 --record
```
```
kubectl rollout history deployment
```
```
kubectl set image deployment/nginx-deploy nginx-deploy=nginx:1.17 --record
```
```
kubectl rollout history deployment
```
14. Create an nginx pod called nginx-resolver using image nginx, expose it internally
with a service called nginx-resolver-service. Test that you are able to look up the
service and pod names from within the cluster. Use the image: busybox:1.28 for dns
lookup. Record results in /root/nginx-yourname.svc and /root/nginx-yourname.pod
```
apiVersion: v1
kind: Service
metadata:
  name: nginx-resolver-service
spec:
  selector:
    app: nginx-resolver
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
```
```
kubectl run --restart=Never --image busybox:1.28 dns -- sleep 1000
IP of the service
kubectl get svc nginx-resolver-service -o=jsonpath='{.spec.clusterIP}'
kubectl exec -it dns -- nslookup 53.17.255.108 > /root/nginx.svc
kubectl exec -it dns -- nslookup
kubectl get svc nginx-resolver-service -o=jsonpath='{.spec.clusterIP}') > /root/nginx.svc
```
15. Create a static pod on node01 called nginx-critical with image nginx. Create this pod
on node01 and make sure that it is recreated/restarted automatically in case of a
failure.
```
ssh node01
cat /var/lib/kubelet/config.yaml | grep stat
apiVersion: v1
kind: Pod
metadata:
  name: nginx-critical
spec:
  containers:
    - name: web
      image: nginx
      ports:
        - name: web
          containerPort: 80
          protocol: TCP
```
16. Create a pod called multi-pod with two containers.
Container 1, name: alpha, image: nginx
Container 2: beta, image: busybox, command sleep 4800.
a. Environment Variables:
i. container 1:
ii. name: alpha
iii. Container 2:
iv. name: beta
```
apiVersion: v1
kind: Pod
metadata:
  name: mc1
spec:
  volumes:
  - name: alpha
    emptyDir: {}
  containers:
  - name: 1
    image: nginx
    volumeMounts:
    - name: beta
      mountPath: /usr/share/nginx/html
  - name: 2
    image: busybox
    volumeMounts:
    - name: html
      mountPath: /html
    command: "sleep 4800"
    args:
      - while true; do
          date >> /html/index.html;
          sleep 1;
```
