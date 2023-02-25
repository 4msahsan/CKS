<pre>
<h1> Create a default deny NetworkPolic </h1>
mahsan@ctrl:$ kubectl get node -o wide
NAME                STATUS   ROLES           AGE   VERSION   INTERNAL-IP    EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION      CONTAINER-RUNTIME
ctrl.srv.world      Ready    control-plane   65d   v1.26.0   192.168.0.25   <none>        Ubuntu 22.04.1 LTS   5.15.0-46-generic   containerd://1.6.14
snode01.srv.world   Ready    <none>          65d   v1.26.0   192.168.0.71   <none>        Ubuntu 22.04.1 LTS   5.15.0-46-generic   containerd://1.6.14
snode02.srv.world   Ready    <none>          65d   v1.26.0   192.168.0.72   <none>        Ubuntu 22.04.1 LTS   5.15.0-46-generic   containerd://1.6.14
mahsan@ctrl:$

<b><h2>
Default NetWork Policy </b></h2>


mahsan@ctrl:$ k run pod-1 --image=nginx
pod/pod-1 created
mahsan@ctrl:$ k run pod-2 --image=nginx
pod/pod-2 created

<b>
kubectl delete pod  --grace-period=0 --force my-release2-mariadb-0
k delete pvc data-my-release2-mariadb-0 --grace-period=0 --force
</b>

mahsan@ctrl:$ k get pod -o wide
NAME                    READY   STATUS    RESTARTS   AGE     IP              NODE                NOMINATED NODE   READINESS GATES
pod-1                   1/1     Running   0          12s     172.16.186.68   snode01.srv.world   <none>           <none>
pod-2                   1/1     Running   0          8s      172.16.186.69   snode01.srv.world   <none>           <none>
mahsan@ctrl:$

mahsan@ctrl:$ k expose pod pod-1 --port 80
service/pod-1 exposed
mahsan@ctrl:$ k expose pod pod-2 --port 80
service/pod-2 exposed
mahsan@ctrl:$ k get all
NAME                        READY   STATUS    RESTARTS   AGE
pod/my-release-mariadb-0    0/1     Pending   0          11m
pod/my-release2-mariadb-0   0/1     Pending   0          10m
pod/pod-1                   1/1     Running   0          4m23s
pod/pod-2                   1/1     Running   0          4m19s

NAME                 TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1        <none>        443/TCP   65d
service/pod-1        ClusterIP   10.100.169.186   <none>        80/TCP    8s
service/pod-2        ClusterIP   10.98.130.23     <none>        80/TCP    4s

NAME                                   READY   AGE
statefulset.apps/my-release-mariadb    0/1     4d13h
statefulset.apps/my-release2-mariadb   0/1     4d13h
mahsan@ctrl:$

<b> Acces to Pod-2  from pod-1</b>
mahsan@ctrl:$ k exec pod-1 -- curl pod-2
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   615  100   615    0     0  76875      0 --:--:-- --:--:-- --:--:-- 76875
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


<b> Acces to Pod-1  from pod-2</b>

mahsan@ctrl:$ k exec pod-2 -- curl pod-1
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   615  100   615    0     0   100k      0 --:--:-- --:--:-- --:--:--  100k
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
mahsan@ctrl:$

mahsan@ctrl:/CKS/NetWorkPolicy$ cat nwpolicy.yml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: test-network-policy
  namespace: default
spec:
  podSelector: {}
  policyTypes:
  - Ingress
  - Egress
mahsan@ctrl:/CKS/NetWorkPolicy$ kubectl apply -f nwpolicy.yml
networkpolicy.networking.k8s.io/test-network-policy created

mahsan@ctrl:/CKS/NetWorkPolicy$ kubectl exec pod-2 -- curl pod-1
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:--  0:00:03 --:--:--     0
mahsan@ctrl:/CKS/NetWorkPolicy$ kubectl exec pod-1 -- curl pod-2
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:--  0:00:01 --:--:--     0
mahsan@ctrl:/CKS/NetWorkPolicy$

<b> Unable to access pods due to new NetworkPolicy </b>


</pre>

