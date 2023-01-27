# Create Certificate Request

## Create a Certificate Request
Certificate needs to be created in istio-system namespace if Istio Ingress load balancer is used for exposing the application.

```shell
kubectl apply -f configurations/Certificate.yaml
```

## Get the status of the Certificate

```shell
kubectl describe certificate -n istio-system bookinfo-tls
...
Spec:
Dns Names:
    bookinfo.nafey.online
Duration:  2160h0m0s
Issuer Ref:
    Group:  cert-manager.io
    Kind:   ClusterIssuer
    Name:   letsencrypt
Private Key:
    Algorithm:   RSA
    Encoding:    PKCS1
    Size:        2048
Renew Before:  360h0m0s
Secret Name:   bookinfo-nafey-online-tls
Subject:
    Organizations:
    nafey.online
Usages:
    server auth
    client auth
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
Normal  Issuing    31s   cert-manager-certificates-trigger          Issuing certificate as Secret does not exist
Normal  Generated  31s   cert-manager-certificates-key-manager      Stored new private key in temporary Secret resource "bookinfo-tls-wg6sr"
Normal  Requested  31s   cert-manager-certificates-request-manager  Created new CertificateRequest resource "bookinfo-tls-6q5fs"
Normal  Issuing    7s    cert-manager-certificates-issuing          The certificate has been successfully issued
```

## Check the Secret

A secret with a private key and a certificate is created

```shell
kubectl describe secret -n istio-system bookinfo-nafey-online-tls
Name:         bookinfo-nafey-online-tls
Namespace:    istio-system
Labels:       controller.cert-manager.io/fao=true
Annotations:  cert-manager.io/alt-names: bookinfo.nafey.online
            cert-manager.io/certificate-name: bookinfo-tls
            cert-manager.io/common-name: bookinfo.nafey.online
            cert-manager.io/ip-sans: 
            cert-manager.io/issuer-group: cert-manager.io
            cert-manager.io/issuer-kind: ClusterIssuer
            cert-manager.io/issuer-name: letsencrypt
            cert-manager.io/uri-sans: 
Type:  kubernetes.io/tls
Data
====
tls.crt:  5607 bytes
tls.key:  1679 bytes

```

### Links
[Certificate](https://cert-manager.io/docs/usage/certificate/#creating-certificate-resources)
[Istio Gateway](https://istio.io/latest/docs/ops/integrations/certmanager/)