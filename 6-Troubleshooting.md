# Troubleshooting


## Check the logs of a cert-manager Pod
In case of an error, the detailed logs of an operations can be viewed by checking the logs of cert-manager pod

```shell
kubectl logs -n cert-manager cert-manager-99bb69456-5p2wp
...
E0127 02:04:00.026339       1 sync.go:73] cert-manager/orders "msg"="failed to update status" "error"=null "resource_kind"="Order" "resource_name"="bookinfo-tls-6q5fs-375348725""resource_namespace"="istio-system" "resource_version"="v1"
I0127 02:04:00.026550       1 controller.go:162] cert-manager/orders "msg"="re-queuing item due to optimistic locking on resource" "error"="Operation cannot be fulfilled on orders.acmecert-manager.io \"bookinfo-tls-6q5fs-375348725\": the object has been modified; please apply your changes to the latest version and try again" "key"="istio-system/bookinfo-tls-6q5fs-375348725"
I0127 02:04:00.596939       1 acme.go:233] cert-manager/certificaterequests-issuer-acme/sign "msg"="certificate issued" "related_resource_kind"="Order""related_resource_name"="bookinfo-tls-6q5fs-375348725" "related_resource_namespace"="istio-system" "related_resource_version"="v1" "resource_kind"="CertificateRequest""resource_name"="bookinfo-tls-6q5fs" "resource_namespace"="istio-system" "resource_version"="v1"
I0127 02:04:00.597124       1 conditions.go:252] Found status change for CertificateRequest "bookinfo-tls-6q5fs" condition "Ready": "False" -> "True"; setting lastTransitionTime to 2023-01-2702:04:00.597115369 +0000 UTC m=+27706.177551651
E0127 02:04:00.646779       1 controller.go:208] cert-manager/challenges "msg"="challenge in work queue no longer exists" "error"="challenge.acme.cert-manager.io\"bookinfo-tls-6q5fs-375348725-2624969334\" not found"
I0127 02:04:00.654884       1 conditions.go:192] Found status change for Certificate "bookinfo-tls" condition "Ready": "False" -> "True"; setting lastTransitionTime to 2023-01-27 02:04:00654874568 +0000 UTC m=+27706.235310850
I0127 02:04:00.695250       1 controller.go:162] cert-manager/certificates-readiness "msg"="re-queuing item due to optimistic locking on resource" "error"="Operation cannot be fulfilled oncertificates.cert-manager.io \"bookinfo-tls\": the object has been modified; please apply your changes to the latest version and try again" "key"="istio-system/bookinfo-tls"
I0127 02:04:00.695782       1 conditions.go:192] Found status change for Certificate "bookinfo-tls" condition "Ready": "False" -> "True"; setting lastTransitionTime to 2023-01-27 02:04:00695774325 +0000 UTC m=+27706.276210617
I0127 02:04:00.734182       1 controller.go:162] cert-manager/certificates-readiness "msg"="re-queuing item due to optimistic locking on resource" "error"="Operation cannot be fulfilled oncertificates.cert-manager.io \"bookinfo-tls\": the object has been modified; please apply your changes to the latest version and try again" "key"="istio-system/bookinfo-tls"
I0127 02:04:00.734813       1 conditions.go:192] Found status change for Certificate "bookinfo-tls" condition "Ready": "False" -> "True"; setting lastTransitionTime to 2023-01-27 02:04:0073480171 +0000 UTC m=+27706.315238002
```

## Check the events of a resource operation

```shell
kubectl describe certificate -n istio-system bookinfo-tls

Status:
Conditions:
    Last Transition Time:  2023-01-27T02:04:00Z
    Message:               Certificate is up to date and has not expired
    Observed Generation:   1
    Reason:                Ready
    Status:                True
    Type:                  Ready
Not After:               2023-04-27T01:03:59Z
Not Before:              2023-01-27T01:04:00Z
Renewal Time:            2023-04-12T01:03:59Z
Revision:                1
Events:
Type    Reason     Age   From                                       Message
----    ------     ----  ----                                       -------
Normal  Issuing    41m   cert-manager-certificates-trigger          Issuing certificate as Secret does not exist
Normal  Generated  41m   cert-manager-certificates-key-manager      Stored new private key in temporary Secret resource "bookinfo-tls-wg6sr"
Normal  Requested  41m   cert-manager-certificates-request-manager  Created new CertificateRequest resource "bookinfo-tls-6q5fs"
Normal  Issuing    41m   cert-manager-certificates-issuing          The certificate has been successfully issued
```

## Links
[Cert Manager - Securing NGINX-ingress](https://cert-manager.io/docs/tutorials/acme/nginx-ingress/)