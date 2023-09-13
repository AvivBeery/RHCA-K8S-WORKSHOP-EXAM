1. Type the command for:  
Get pods with label information 
```
kubectl get pods --show-labels
```
2. Create 5 nginx pods in which two of them is labeled env=prod and three of them is 
labeled env=dev
```
kubectl run nginx-dev1 --image=nginx --restart=Never --labels=env=dev
kubectl run nginx-dev2 --image=nginx --restart=Never --labels=env=dev
kubectl run nginx-dev3 --image=nginx --restart=Never --labels=env=dev
kubectl run nginx-prod1 --image=nginx --restart=Never --labels=env=prod
kubectl run nginx-prod2 --image=nginx --restart=Never --labels=env=prod
```
4. Verify all the pods are created with correct labels 
```
kubectl get pods --show-labels
```
5. Get the pods with label env=dev 
```
kubectl get pods -l env=dev
```
6. Get the pods with label env=dev and also output the labels 
```
kubectl get pods -l env=dev --show-labels
```
7. Get the pods with label env=prod 
```
kubectl get pods -l env=prod
```
8. Get the pods with label env=prod and also output the labels
```
kubectl get pods -l env=prod --show-labels
```
9. Get the pods with label env 
```
kubectl get pods -L env
```
10. Get the pods with labels env=dev and env=prod
```
kubectl get pods -l 'env in (dev,prod)'
```
11. Get the pods with labels env=dev and env=prod and output the labels as well 
```
kubectl get pods -l 'env in (dev,prod)' --show-labels
```
12. Change the label for one of the pod to env=uat and list all the pods to verify 
```
kubectl label pod/nginx-2dev env=uat --overwrite
kubectl get pods --show-labels
```
13. Remove the labels for the pods that we created now and verify all the labels are 
removed
```
kubectl label pod nginx-dev{1..3} env-
kubectl label pod nginx-prod{1..2} env-
kubectl get po --show-labels
```
14. Let’s add the label app=nginx for all the pods and verify (using kubectl) 
```
kubectl label pod nginx-dev{1..3} app=nginx
kubectl label pod nginx-prod{1..2} app=nginx
kubectl get po --show-labels
```
15. Get all the nodes with labels (if using minikube you would get only master node) 
```
kubectl get nodes --show-labels
```
16. Label the worker node nodeName=nginxnode
```
kubectl label node i-066ace7c4286ab059 nodeName=nginxnode
```
17. Create a Pod that will be deployed on the worker node with the label 
```
nodeName=nginxnode 
```
Add the nodeSelector to the below and create the pod
```
vi pod.yaml
```
add the nodeSelector like below and create the pod
```
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx
spec:
  nodeSelector:
    nodeName: nginxnode
  containers:
  - image: nginx
    name: nginx
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```
```
kubectl run nginx --image=nginx --restart=Never --dry-run -o yaml & pod.yaml
```
```
kubectl create -f pod.yaml
```
17. Verify the pod that it is scheduled with the node selector on the right node… fix it if 
it’s not behind scheduled.  
```
kubectl describe po nginx | grep Node-Selectors
```
NAME          READY   STATUS    RESTARTS   AGE
nginx         1/1     Running   0          18m

18. Verify the pod nginx that we just created has this label 
```
kubectl describe po nginx | grep Labels
```
Labels:           run=nginx
