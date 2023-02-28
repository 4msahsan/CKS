<pre>
<h1> Secure an Ingress </h1>

root@microk8s:~/CKS# curl https://192.168.0.75:32531/service2
curl: (60) SSL certificate problem: self-signed certificate
More details here: https://curl.se/docs/sslcerts.html

curl failed to verify the legitimacy of the server and therefore could not
establish a secure connection to it. To learn more about this situation and
how to fix it, please visit the web page mentioned above.
root@microk8s:~/CKS#

<b>root@microk8s:~/CKS# curl https://192.168.0.75:32531/service2 -kv</b>
*   Trying 192.168.0.75:32531...
* Connected to 192.168.0.75 (192.168.0.75) port 32531 (#0)
* ALPN, offering h2
* ALPN, offering http/1.1
* TLSv1.0 (OUT), TLS header, Certificate Status (22):
* TLSv1.3 (OUT), TLS handshake, Client hello (1):
* TLSv1.2 (IN), TLS header, Certificate Status (22):
* TLSv1.3 (IN), TLS handshake, Server hello (2):
* TLSv1.2 (IN), TLS header, Finished (20):
* TLSv1.2 (IN), TLS header, Supplemental data (23):
* TLSv1.3 (IN), TLS handshake, Encrypted Extensions (8):
* TLSv1.2 (IN), TLS header, Supplemental data (23):
* TLSv1.3 (IN), TLS handshake, Certificate (11):
* TLSv1.2 (IN), TLS header, Supplemental data (23):
* TLSv1.3 (IN), TLS handshake, CERT verify (15):
* TLSv1.2 (IN), TLS header, Supplemental data (23):
* TLSv1.3 (IN), TLS handshake, Finished (20):
* TLSv1.2 (OUT), TLS header, Finished (20):
* TLSv1.3 (OUT), TLS change cipher, Change cipher spec (1):
* TLSv1.2 (OUT), TLS header, Supplemental data (23):
* TLSv1.3 (OUT), TLS handshake, Finished (20):
* SSL connection using TLSv1.3 / TLS_AES_256_GCM_SHA384
* ALPN, server accepted to use h2
* Server certificate:
<b>*  subject: O=Acme Co; CN=Kubernetes Ingress Controller Fake Certificate</b>
*  start date: Feb 27 19:51:09 2023 GMT
*  expire date: Feb 27 19:51:09 2024 GMT
<b>  issuer: O=Acme Co; CN=Kubernetes Ingress Controller Fake Certificate </b>
*  SSL certificate verify result: self-signed certificate (18), continuing anyway.
* Using HTTP2, server supports multiplexing
* Connection state changed (HTTP/2 confirmed)
* Copying HTTP/2 data in stream buffer to connection buffer after upgrade: len=0
* TLSv1.2 (OUT), TLS header, Supplemental data (23):
* TLSv1.2 (OUT), TLS header, Supplemental data (23):
* TLSv1.2 (OUT), TLS header, Supplemental data (23):
* Using Stream ID: 1 (easy handle 0x55e6a6371c60)
* TLSv1.2 (OUT), TLS header, Supplemental data (23):
> GET /service2 HTTP/2
> Host: 192.168.0.75:32531
> user-agent: curl/7.81.0
> accept: */*
>
* TLSv1.2 (IN), TLS header, Supplemental data (23):
* TLSv1.3 (IN), TLS handshake, Newsession Ticket (4):
* TLSv1.2 (IN), TLS header, Supplemental data (23):
* TLSv1.3 (IN), TLS handshake, Newsession Ticket (4):
* old SSL session ID is stale, removing
* TLSv1.2 (IN), TLS header, Supplemental data (23):
* Connection state changed (MAX_CONCURRENT_STREAMS == 128)!
* TLSv1.2 (OUT), TLS header, Supplemental data (23):
* TLSv1.2 (IN), TLS header, Supplemental data (23):
< HTTP/2 200
< date: Tue, 28 Feb 2023 05:07:28 GMT
< content-type: text/html
< content-length: 45
< last-modified: Mon, 11 Jun 2007 18:53:14 GMT
< etag: "2d-432a5e4a73a80"
< accept-ranges: bytes
< strict-transport-security: max-age=15724800; includeSubDomains
<
<html><body><h1>It works!</h1></body></html>
* TLSv1.2 (IN), TLS header, Supplemental data (23):
* Connection #0 to host 192.168.0.75 left intact
root@microk8s:~/CKS#
<b>
root@microk8s:~/CKS/Ingress/TLS# openssl req -x509 -newkey rsa:4096 -keyout ke<b><y.pem -out cert.pem -days 365 -nodes </b>
......+.........+.+......+.....+......+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*...+.........+......+....+......+.....+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*..+.....+.......+...+........+......+...+......+.......+........+....+...........+.......+.................+.+.....+................+........................+.........+...+........+.+...+........+...............+.........+......+...+..........+..+...+.........+.+.....+.......+.........+...+..+...+.......+......+.....+.............+...........+...+...+....

You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [AU]:CA
State or Province Name (full name) [Some-State]:BC
Locality Name (eg, city) []:Vancouver
Organization Name (eg, company) [Internet Widgits Pty Ltd]:ABC group
Organizational Unit Name (eg, section) []:ABC123
Common Name (e.g. server FQDN or YOUR name) []:secure-ingress.com
Email Address []:msahsan@hotmail.com

<b>
root@microk8s:~/CKS/Ingress/TLS# kk create secret tls secure-ingress --cert=cert.pem --key=key.pem </b>
secret/secure-ingress created

root@microk8s:~/CKS/Ingress/TLS# kk get secret
NAME                              TYPE                 DATA   AGE
wordpress                         Opaque               1      5d10h
wordpress-mariadb                 Opaque               2      5d10h
sh.helm.release.v1.wordpress.v1   helm.sh/release.v1   1      5d10h
secure-ingress                    kubernetes.io/tls    2      24s
root@microk8s:~/CKS/Ingress/TLS#

root@microk8s:~/CKS/Ingress/TLS# kk describe secret secure-ingress
Name:         secure-ingress
Namespace:    default
Labels:       <none>
Annotations:  <none>

Type:  kubernetes.io/tls

Data
====
tls.crt:  2155 bytes
tls.key:  3272 bytes
root@microk8s:~/CKS/Ingress/TLS

root@microk8s:~/CKS/Ingress/TLS# cat ingress.yml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: secure-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  tls:
  - hosts:
      - secure-ingress.com
    secretName: secure-ingress
  rules:
  - host: secure-ingress.com
    http:
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

root@microk8s:~/CKS/Ingress/TLS#


root@microk8s:~/CKS/Ingress/TLS#

root@microk8s:~/CKS/Ingress/TLS# kk get ingress
NAME              CLASS    HOSTS   ADDRESS        PORTS     AGE
minimal-ingress   public   *       192.168.0.75   80, 443   7h27m
root@microk8s:~/CKS/Ingress/TLS# kk describe  ingress minimal-ingress
Name:             minimal-ingress
Labels:           <none>
Namespace:        default
Address:          127.0.0.1
Ingress Class:    public
Default backend:  <default>
TLS:
  secure-ingress terminates secure-ingress.com
Rules:
  Host        Path  Backends
  ----        ----  --------
  *
              /service1   service1:80 (10.1.128.224:80)
              /service2   service2:80 (10.1.128.225:80)
Annotations:  nginx.ingress.kubernetes.io/rewrite-target: /
Events:
  Type    Reason  Age                      From                      Message
  ----    ------  ----                     ----                      -------
  Normal  Sync    2m51s (x893 over 7h28m)  nginx-ingress-controller  Scheduled for sync
  Normal  Sync    2m51s (x893 over 7h28m)  nginx-ingress-controller  Scheduled for sync
root@microk8s:~/CKS/Ingress/TLS#

root@microk8s:~/CKS/Ingress/TLS# curl https://192.168.0.75:32531/service2 -kv
*   Trying 192.168.0.75:32531...
* Connected to 192.168.0.75 (192.168.0.75) port 32531 (#0)
* ALPN, offering h2
* ALPN, offering http/1.1
* TLSv1.0 (OUT), TLS header, Certificate Status (22):
* TLSv1.3 (OUT), TLS handshake, Client hello (1):
* TLSv1.2 (IN), TLS header, Certificate Status (22):
* TLSv1.3 (IN), TLS handshake, Server hello (2):
* TLSv1.2 (IN), TLS header, Finished (20):
* TLSv1.2 (IN), TLS header, Supplemental data (23):
* TLSv1.3 (IN), TLS handshake, Encrypted Extensions (8):
* TLSv1.2 (IN), TLS header, Supplemental data (23):
* TLSv1.3 (IN), TLS handshake, Certificate (11):
* TLSv1.2 (IN), TLS header, Supplemental data (23):
* TLSv1.3 (IN), TLS handshake, CERT verify (15):
* TLSv1.2 (IN), TLS header, Supplemental data (23):
* TLSv1.3 (IN), TLS handshake, Finished (20):
* TLSv1.2 (OUT), TLS header, Finished (20):
* TLSv1.3 (OUT), TLS change cipher, Change cipher spec (1):
* TLSv1.2 (OUT), TLS header, Supplemental data (23):
* TLSv1.3 (OUT), TLS handshake, Finished (20):
* SSL connection using TLSv1.3 / TLS_AES_256_GCM_SHA384
* ALPN, server accepted to use h2
* Server certificate:
*  subject: O=Acme Co; CN=Kubernetes Ingress Controller Fake Certificate
*  start date: Feb 27 19:51:09 2023 GMT
*  expire date: Feb 27 19:51:09 2024 GMT
*  issuer: O=Acme Co; CN=Kubernetes Ingress Controller Fake Certificate
*  SSL certificate verify result: self-signed certificate (18), continuing anyway.
* Using HTTP2, server supports multiplexing
* Connection state changed (HTTP/2 confirmed)
* Copying HTTP/2 data in stream buffer to connection buffer after upgrade: len=0
* TLSv1.2 (OUT), TLS header, Supplemental data (23):
* TLSv1.2 (OUT), TLS header, Supplemental data (23):
* TLSv1.2 (OUT), TLS header, Supplemental data (23):
* Using Stream ID: 1 (easy handle 0x55e8f872fc60)
* TLSv1.2 (OUT), TLS header, Supplemental data (23):
> GET /service2 HTTP/2
> Host: 192.168.0.75:32531
> user-agent: curl/7.81.0
> accept: */*
>
* TLSv1.2 (IN), TLS header, Supplemental data (23):
* TLSv1.3 (IN), TLS handshake, Newsession Ticket (4):
* TLSv1.2 (IN), TLS header, Supplemental data (23):
* TLSv1.3 (IN), TLS handshake, Newsession Ticket (4):
* old SSL session ID is stale, removing
* TLSv1.2 (IN), TLS header, Supplemental data (23):
* Connection state changed (MAX_CONCURRENT_STREAMS == 128)!
* TLSv1.2 (OUT), TLS header, Supplemental data (23):
* TLSv1.2 (IN), TLS header, Supplemental data (23):
< HTTP/2 200
< date: Tue, 28 Feb 2023 05:31:02 GMT
< content-type: text/html
< content-length: 45
< last-modified: Mon, 11 Jun 2007 18:53:14 GMT
< etag: "2d-432a5e4a73a80"
< accept-ranges: bytes
< strict-transport-security: max-age=15724800; includeSubDomains
<
<html><body><h1>It works!</h1></body></html>
* TLSv1.2 (IN), TLS header, Supplemental data (23):
* Connection #0 to host 192.168.0.75 left intact
root@microk8s:~/CKS/Ingress/TLS#


<b>root@microk8s:~/CKS/Ingress/TLS# curl https://secure-ingress.com:32531/service1 -kv </b>
*   Trying 192.168.0.75:32531...
* Connected to secure-ingress.com (192.168.0.75) port 32531 (#0)
* ALPN, offering h2
* ALPN, offering http/1.1
* TLSv1.0 (OUT), TLS header, Certificate Status (22):
* TLSv1.3 (OUT), TLS handshake, Client hello (1):
* TLSv1.2 (IN), TLS header, Certificate Status (22):
* TLSv1.3 (IN), TLS handshake, Server hello (2):
* TLSv1.2 (IN), TLS header, Finished (20):
* TLSv1.2 (IN), TLS header, Supplemental data (23):
* TLSv1.3 (IN), TLS handshake, Encrypted Extensions (8):
* TLSv1.2 (IN), TLS header, Supplemental data (23):
* TLSv1.3 (IN), TLS handshake, Certificate (11):
* TLSv1.2 (IN), TLS header, Supplemental data (23):
* TLSv1.3 (IN), TLS handshake, CERT verify (15):
* TLSv1.2 (IN), TLS header, Supplemental data (23):
* TLSv1.3 (IN), TLS handshake, Finished (20):
* TLSv1.2 (OUT), TLS header, Finished (20):
* TLSv1.3 (OUT), TLS change cipher, Change cipher spec (1):
* TLSv1.2 (OUT), TLS header, Supplemental data (23):
* TLSv1.3 (OUT), TLS handshake, Finished (20):
* SSL connection using TLSv1.3 / TLS_AES_256_GCM_SHA384
* ALPN, server accepted to use h2
* Server certificate:
*  subject: C=AU; ST=Some-State; O=Internet Widgits Pty Ltd; CN=secure-ingress.com
*  start date: Feb 28 05:53:40 2023 GMT
*  expire date: Feb 28 05:53:40 2024 GMT
*  issuer: C=AU; ST=Some-State; O=Internet Widgits Pty Ltd; CN=secure-ingress.com
*  SSL certificate verify result: self-signed certificate (18), continuing anyway.
* Using HTTP2, server supports multiplexing
* Connection state changed (HTTP/2 confirmed)
* Copying HTTP/2 data in stream buffer to connection buffer after upgrade: len=0
* TLSv1.2 (OUT), TLS header, Supplemental data (23):
* TLSv1.2 (OUT), TLS header, Supplemental data (23):
* TLSv1.2 (OUT), TLS header, Supplemental data (23):
* Using Stream ID: 1 (easy handle 0x5641dffa0c60)
* TLSv1.2 (OUT), TLS header, Supplemental data (23):
> GET /service1 HTTP/2
> Host: secure-ingress.com:32531
> user-agent: curl/7.81.0
> accept: */*
>
* TLSv1.2 (IN), TLS header, Supplemental data (23):
* TLSv1.3 (IN), TLS handshake, Newsession Ticket (4):
* TLSv1.2 (IN), TLS header, Supplemental data (23):
* TLSv1.3 (IN), TLS handshake, Newsession Ticket (4):
* old SSL session ID is stale, removing
* TLSv1.2 (IN), TLS header, Supplemental data (23):
* Connection state changed (MAX_CONCURRENT_STREAMS == 128)!
* TLSv1.2 (OUT), TLS header, Supplemental data (23):
* TLSv1.2 (IN), TLS header, Supplemental data (23):
< HTTP/2 200
< date: Tue, 28 Feb 2023 06:00:05 GMT
< content-type: text/html
< content-length: 615
< last-modified: Tue, 13 Dec 2022 15:53:53 GMT
< etag: "6398a011-267"
< accept-ranges: bytes
< strict-transport-security: max-age=15724800; includeSubDomains
<
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
Welcome to nginx!
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
* TLSv1.2 (IN), TLS header, Supplemental data (23):
* Connection #0 to host secure-ingress.com left intact
root@microk8s:~/CKS/Ingress/TLS#
<b>
root@microk8s:~/CKS/Ingress/TLS# curl https://secure-ingress.com:32531/service2 -kv </b>
*   Trying 192.168.0.75:32531...
* Connected to secure-ingress.com (192.168.0.75) port 32531 (#0)
* ALPN, offering h2
* ALPN, offering http/1.1
* TLSv1.0 (OUT), TLS header, Certificate Status (22):
* TLSv1.3 (OUT), TLS handshake, Client hello (1):
* TLSv1.2 (IN), TLS header, Certificate Status (22):
* TLSv1.3 (IN), TLS handshake, Server hello (2):
* TLSv1.2 (IN), TLS header, Finished (20):
* TLSv1.2 (IN), TLS header, Supplemental data (23):
* TLSv1.3 (IN), TLS handshake, Encrypted Extensions (8):
* TLSv1.2 (IN), TLS header, Supplemental data (23):
* TLSv1.3 (IN), TLS handshake, Certificate (11):
* TLSv1.2 (IN), TLS header, Supplemental data (23):
* TLSv1.3 (IN), TLS handshake, CERT verify (15):
* TLSv1.2 (IN), TLS header, Supplemental data (23):
* TLSv1.3 (IN), TLS handshake, Finished (20):
* TLSv1.2 (OUT), TLS header, Finished (20):
* TLSv1.3 (OUT), TLS change cipher, Change cipher spec (1):
* TLSv1.2 (OUT), TLS header, Supplemental data (23):
* TLSv1.3 (OUT), TLS handshake, Finished (20):
* SSL connection using TLSv1.3 / TLS_AES_256_GCM_SHA384
* ALPN, server accepted to use h2
* Server certificate:
*  subject: C=AU; ST=Some-State; O=Internet Widgits Pty Ltd; CN=secure-ingress.com
*  start date: Feb 28 05:53:40 2023 GMT
*  expire date: Feb 28 05:53:40 2024 GMT
*  issuer: C=AU; ST=Some-State; O=Internet Widgits Pty Ltd; CN=secure-ingress.com
*  SSL certificate verify result: self-signed certificate (18), continuing anyway.
* Using HTTP2, server supports multiplexing
* Connection state changed (HTTP/2 confirmed)
* Copying HTTP/2 data in stream buffer to connection buffer after upgrade: len=0
* TLSv1.2 (OUT), TLS header, Supplemental data (23):
* TLSv1.2 (OUT), TLS header, Supplemental data (23):
* TLSv1.2 (OUT), TLS header, Supplemental data (23):
* Using Stream ID: 1 (easy handle 0x564b4a91ac60)
* TLSv1.2 (OUT), TLS header, Supplemental data (23):
> GET /service2 HTTP/2
> Host: secure-ingress.com:32531
> user-agent: curl/7.81.0
> accept: */*
>
* TLSv1.2 (IN), TLS header, Supplemental data (23):
* TLSv1.3 (IN), TLS handshake, Newsession Ticket (4):
* TLSv1.2 (IN), TLS header, Supplemental data (23):
* TLSv1.3 (IN), TLS handshake, Newsession Ticket (4):
* old SSL session ID is stale, removing
* TLSv1.2 (IN), TLS header, Supplemental data (23):
* Connection state changed (MAX_CONCURRENT_STREAMS == 128)!
* TLSv1.2 (OUT), TLS header, Supplemental data (23):
* TLSv1.2 (IN), TLS header, Supplemental data (23):
* TLSv1.2 (IN), TLS header, Supplemental data (23):
< HTTP/2 200
< date: Tue, 28 Feb 2023 06:01:53 GMT
< content-type: text/html
< content-length: 45
< last-modified: Mon, 11 Jun 2007 18:53:14 GMT
< etag: "2d-432a5e4a73a80"
< accept-ranges: bytes
< strict-transport-security: max-age=15724800; includeSubDomains
<
<html><body>It works!></body></html>
* TLSv1.2 (IN), TLS header, Supplemental data (23):
* Connection #0 to host secure-ingress.com left intact
root@microk8s:~/CKS/Ingress/TLS#

</pre>
