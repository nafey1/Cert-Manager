# Istio Gateway and Virtual Service 

## Create or Modify the Bookinfo gateway as follows
The gateway must be created in the istio-system namespace, where the Ingress Load Balancer is created. The Virtual Service and Destination Rules can reside in the application namespace.

```shell
kubectl apply -f configurations/istio-gw-vs.yaml
```

## Modify the Virtual Service
Notice the Virtual Service resides in the application namespace but references the gateway in the istio-system namespace.

```shell
kind: VirtualService
apiVersion: networking.istio.io/v1beta1
metadata:
  name: bookinfo
  namespace: bookinfo
spec:
  hosts:
    - bookinfo.nafey.online
  gateways:
    - istio-system/bookinfo-gateway
```

## Verification
Notice that curl hits the unencrypted URL and gets a redirection HTTP/1.1 301 message

```shell
curl --verbose http://bookinfo.nafey.online/productpage
*   Trying 140.238.132.203:80...
* Connected to bookinfo.nafey.online (140.238.132.203) port 80 (#0)
> GET /productpage HTTP/1.1
> Host: bookinfo.nafey.online
> User-Agent: curl/7.85.0
> Accept: */*
> 
* Mark bundle as not supporting multiuse
< HTTP/1.1 301 Moved Permanently
< location: https://bookinfo.nafey.online/productpage
< date: Fri, 27 Jan 2023 02:25:41 GMT
< server: istio-envoy
< content-length: 0
< 
* Connection #0 to host bookinfo.nafey.online left intact
```