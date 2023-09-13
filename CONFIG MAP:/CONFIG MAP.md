1. Create a file called config.txt with two values key1=value1 and key2=value2 and 
verify the file
```
cat >> config.txt << EOF 
key1=value1 
key2=value2 
EOF 
cat config.txt
```
2.  Create a configmap named keyvalcfgmap and read data from the file config.txt and 
verify that configmap is created correctly
```
kubectl create cm keyvalcfgmap --from-file=config.txt
```
configmap/keyvalcfgmap created
```
kubectl get cm keyvalcfgmap -o yaml
```
```
apiVersion: v1
data:
  config.txt: " \nkey1=value1 \n\nkey2=value2 \n\n"
kind: ConfigMap
metadata:
  creationTimestamp: "2023-09-13T15:06:06Z"
  name: keyvalcfgmap
  namespace: default
  resourceVersion: "4273"
  uid: d991662b-b982-475f-b074-7176e8c3ee1b
```
3. Create an nginx pod and load environment values from the above configmap 
keyvalcfgmap and exec into the pod and verify the environment variables and delete 
the pod 
// first run this command to save the pod yml 
kubectl run nginx --image=nginx --restart=Never --dry-run -o yaml > nginx-pod.yml

edit the nginx-pod.yml to below file and create with
```
vi nginx-pod.yml
```
```
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx
spec:
  containers:
  - image: nginx
    name: nginx
    resources: {}
    envFrom:
    - configMapRef:
        name: keyvalcfgmap
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```
```
kubectl create -f nginx-pod.yml
```
verify:
```
kubectl exec -it nginx -- env
```
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOSTNAME=nginx
NGINX_VERSION=1.25.2
NJS_VERSION=0.8.0
PKG_RELEASE=1~bookworm
config.txt= 
key1=value1 

key2=value2 


KUBERNETES_PORT_443_TCP_PROTO=tcp
KUBERNETES_PORT_443_TCP_PORT=443
KUBERNETES_PORT_443_TCP_ADDR=100.64.0.1
KUBERNETES_SERVICE_HOST=100.64.0.1
KUBERNETES_SERVICE_PORT=443
KUBERNETES_SERVICE_PORT_HTTPS=443
KUBERNETES_PORT=tcp://100.64.0.1:443
KUBERNETES_PORT_443_TCP=tcp://100.64.0.1:443
TERM=xterm
HOME=/root
`
kubectl delete po nginx
pod "nginx" deleted
