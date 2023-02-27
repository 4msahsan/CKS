<pre>
<h1> create an Ingress </h1>

root@microk8s:~# kk get ns
NAME              STATUS   AGE
kube-system       Active   5d3h
kube-public       Active   5d3h
kube-node-lease   Active   5d3h
default           Active   5d3h
metallb-system    Active   5d3h
ingress           Active   2d5h
ingress-nginx     Active   2d5h
root@microk8s:~# kk get all -n ingress-nginx
NAME                                            READY   STATUS      RESTARTS       AGE
pod/ingress-nginx-admission-create-zsndx        0/1     Completed   0              2d5h
pod/ingress-nginx-admission-patch-zgmcc         0/1     Completed   0              2d5h
pod/ingress-nginx-controller-6955786b87-94jrn   1/1     Running     1 (121m ago)   2d5h

NAME                                         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                      AGE
service/ingress-nginx-controller-admission   ClusterIP   10.152.183.254   <none>        443/TCP                      2d5h
service/ingress-nginx-controller             NodePort    10.152.183.127   <none>        80:32393/TCP,443:32531/TCP   2d5h

NAME                                       READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/ingress-nginx-controller   1/1     1            1           2d5h

NAME                                                  DESIRED   CURRENT   READY   AGE
replicaset.apps/ingress-nginx-controller-6955786b87   1         1         1       2d5h

NAME                                       COMPLETIONS   DURATION   AGE
job.batch/ingress-nginx-admission-create   1/1           9s         2d5h
job.batch/ingress-nginx-admission-patch    1/1           9s         2d5h
root@microk8s:~#

root@microk8s:~# kk get node -o wide
NAME       STATUS   ROLES    AGE    VERSION   INTERNAL-IP    EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION      CONTAINER-RUNTIME
microk8s   Ready    <none>   5d3h   v1.26.1   192.168.0.75   <none>        Ubuntu 22.04.1 LTS   5.15.0-60-generic   containerd://1.6.8
root@microk8s:~# curl 192.168.0.75:32393
<html>
<head><title>404 Not Found</title></head>
<body>
<center><h1>404 Not Found</h1></center>
<hr><center>nginx</center>
</body>
</html>
root@microk8s:~#

root@microk8s:~# vim ingress.yml
root@microk8s:~# kk run pod1 --image=nginx
pod/pod1 created
root@microk8s:~# kk run pod2 --image=httpd
pod/pod2 created
root@microk8s:~# kk apply -f ingress.yml
ingress.networking.k8s.io/minimal-ingress created

root@microk8s:~# cat ingress.yml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: minimal-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - http:
      paths:
      - path: /service1
        pathType: Prefix
        backend:
          service:
            name: service1
            port:
              number: 80
      - path: /service2
        pathType: Prefix
        backend:
          service:
            name: service2
            port:
              number: 80

root@microk8s:~# kk describe  ingress minimal-ingress
Name:             minimal-ingress
Labels:           <none>
Namespace:        default
Address:          192.168.0.75
Ingress Class:    public
Default backend:  <default>
Rules:
  Host        Path  Backends
  ----        ----  --------
  *
              /service1   service1:80 (10.1.128.224:80)
              /service2   service2:80 (10.1.128.225:80)
Annotations:  nginx.ingress.kubernetes.io/rewrite-target: /
Events:
  Type    Reason  Age                 From                      Message
  ----    ------  ----                ----                      -------
  Normal  Sync    20s (x83 over 40m)  nginx-ingress-controller  Scheduled for sync
  Normal  Sync    20s (x83 over 40m)  nginx-ingress-controller  Scheduled for sync
root@microk8s:~#




root@microk8s:~# kk expose pod pod1 --port 80 --name service1
service/service1 exposed
root@microk8s:~# kk expose pod pod2 --port 80 --name service2
service/service2 exposed
root@microk8s:~# kk get svc
NAME                          TYPE           CLUSTER-IP       EXTERNAL-IP     PORT(S)                      AGE
kubernetes                    ClusterIP      10.152.183.1     <none>          443/TCP                      5d3h
test-nginx-6b4d69f6f8-7phms   LoadBalancer   10.152.183.181   192.168.0.240   80:32043/TCP                 5d3h
wordpress-mariadb             ClusterIP      10.152.183.33    <none>          3306/TCP                     5d3h
wordpress                     LoadBalancer   10.152.183.156   192.168.0.241   80:30933/TCP,443:32766/TCP   5d3h
myapp                         NodePort       10.152.183.166   <none>          80:30063/TCP                 2d5h
service1                      ClusterIP      10.152.183.149   <none>          80/TCP                       20s
service2                      ClusterIP      10.152.183.147   <none>          80/TCP                       5s
root@microk8s:~#


root@microk8s:~# curl 192.168.0.75:32393/service1
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
<b>Welcome to nginx!</b>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
root@microk8s:~# curl 192.168.0.75:32393/service2
<html><body><h1>It works!</h1></body></html>
root@microk8s:~#

</pre>

![Alt text](https://github.com/4msahsan/CKS/blob/master/Ingress/01.png "msahsan@hotmail.com")
