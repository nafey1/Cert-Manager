# Nginx Ingress Configuration

## Install Kubernetes Nginx Ingress Controller

Get the latest manifest file from the link.
[Installation Guide](https://kubernetes.github.io/ingress-nginx/deploy/#oracle-cloud-infrastructure)

```shell
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.5.1/deploy/static/provider/cloud/deploy.yaml
```


### Verify the Service

```shell
kubectl get svc -n ingress-nginx
NAME                                 TYPE           CLUSTER-IP      EXTERNAL-IP      PORT(S)                      AGE
ingress-nginx-controller             LoadBalancer   10.96.65.187    140.238.137.13   80:31161/TCP,443:32157/TCP   39s
ingress-nginx-controller-admission   ClusterIP      10.96.126.253   <none>           443/TCP                      39s
```
## Assign a DNS name

Ensure that the DNS entry points to the IP address of the ingress controller

```shell
nslookup app.nafey.online
Server:		167.206.10.178
Address:	167.206.10.178#53

Non-authoritative answer:
Name:	app.nafey.online
Address: 140.238.137.13
```

## Deploy an Sample Application

The sample application deploys deployment and a service in default namespace

```shell
kubectl apply -f https://raw.githubusercontent.com/cert-manager/website/master/content/docs/tutorials/acme/example/deployment.yaml

kubectl apply -f https://raw.githubusercontent.com/cert-manager/website/master/content/docs/tutorials/acme/example/service.yaml
```

## Deploy an Ingress

Deploy an ingress and keep the annotation commented out for now 

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: kuard
  annotations:
    kubernetes.io/ingress.class: "nginx"    
    #cert-manager.io/issuer: "letsencrypt"

spec:
  tls:
  - hosts:
    - app.nafey.online
    secretName: app-nafey-online-tls
  rules:
  - host: app.nafey.online
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: kuard
            port:
              number: 80
```

Verify the Ingress 

```shell
kubectl get ingress
NAME    CLASS    HOSTS              ADDRESS          PORTS     AGE
kuard   <none>   app.nafey.online   140.238.137.13   80, 443   2m18s
```

## Configure a Let's Encrypt Issuer

Create a Local Issuer in the default namespace. Adjust the ACME server address to either staging or production appropriately


```yaml
apiVersion: cert-manager.io/v1
kind: Issuer
metadata:
    name: letsencrypt
spec:
    acme:
        # The ACME server URL
        server: https://acme-v02.api.letsencrypt.org/directory
        # Email address used for ACME registration
        email: farooqnafey@gmail.com
        # Name of a secret used to store the ACME account private key
        privateKeySecretRef:
            name: letsencrypt
        # Enable the HTTP-01 challenge provider
        solvers:
        - http01:
            ingress:
            class:  nginx
```

### Verify the Issuer

```shell
kubectl get issuer -o wide
NAME          READY   STATUS                                                 AGE
letsencrypt   True    The ACME account was registered with the ACME server   69s
```

## Deploy a TLS Ingress Resource

With all the prerequisite configuration in place, we can now do the pieces to request the TLS certificate. There are two primary ways to do this: using annotations on the ingress with ingress-shim or directly creating a certificate resource.

In this example, we will add annotations to the ingress, and take advantage of ingress-shim to have it create the certificate resource on our behalf. After creating a certificate, the cert-manager will update or create a ingress resource and use that to validate the domain. Once verified and issued, cert-manager will create or update the secret defined in the certificate.

> Reapply the Ingress manifest file created earlier after uncommenting the annotation for cert-manager

```shell
kubectl apply -f Ingress.yaml
```

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: kuard
  annotations:
    kubernetes.io/ingress.class: "nginx"    
    cert-manager.io/issuer: "letsencrypt"

spec:
  tls:
  - hosts:
    - app.nafey.online
    secretName: app-nafey-online-tls
  rules:
  - host: app.nafey.online
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: kuard
            port:
              number: 80
```

Verify the Certificate

```shell
kubectl get certificate -o wide
NAME                   READY   SECRET                 ISSUER        STATUS                                          AGE
app-nafey-online-tls   True    app-nafey-online-tls   letsencrypt   Certificate is up to date and has not expired   41s
```


## Verify the Application TLS

A 308 Permanent Redirect message is received indicating that the request  has been permanently moved to another encrypted URI.

```shell
curl --verbose app.nafey.online
*   Trying 140.238.137.13:80...
* Connected to app.nafey.online (140.238.137.13) port 80 (#0)
> GET / HTTP/1.1
> Host: app.nafey.online
> User-Agent: curl/7.85.0
> Accept: */*
>
* Mark bundle as not supporting multiuse
< HTTP/1.1 308 Permanent Redirect
< Date: Fri, 27 Jan 2023 22:57:11 GMT
< Content-Type: text/html
< Content-Length: 164
< Connection: keep-alive
< Location: https://app.nafey.online
<
<html>
<head><title>308 Permanent Redirect</title></head>
<body>
<center><h1>308 Permanent Redirect</h1></center>
<hr><center>nginx</center>
</body>
</html>
* Connection #0 to host app.nafey.online left intact
```