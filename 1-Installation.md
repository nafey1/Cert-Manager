# Installation


## Default static install (using Manifest file)

```shell
kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.11.0/cert-manager.yaml
```

## Helm Install

```shell
helm repo add jetstack https://charts.jetstack.io
helm repo update
helm install \
cert-manager jetstack/cert-manager \
--namespace cert-manager \
--create-namespace \
--version v1.11.0 \
--set installCRDs=true
```

## Check the Deployment Status

```shell
❯ kubectl get all -n cert-manager
NAME                                          READY   STATUS    RESTARTS   AGE
pod/cert-manager-99bb69456-km2p5              1/1     Running   0          29s
pod/cert-manager-cainjector-ffb4747bb-jfc7n   1/1     Running   0          29s
pod/cert-manager-webhook-545bd5d7d8-qjpcz     1/1     Running   0          29s
NAME                           TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)    AGE
service/cert-manager           ClusterIP   10.96.244.88   <none>        9402/TCP   29s
service/cert-manager-webhook   ClusterIP   10.96.154.64   <none>        443/TCP    29s
NAME                                      READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/cert-manager              1/1     1            1           29s
deployment.apps/cert-manager-cainjector   1/1     1            1           29s
deployment.apps/cert-manager-webhook      1/1     1            1           29s
NAME                                                DESIRED   CURRENT   READY   AGE
replicaset.apps/cert-manager-99bb69456              1         1         1       29s
replicaset.apps/cert-manager-cainjector-ffb4747bb   1         1         1       29s
replicaset.apps/cert-manager-webhook-545bd5d7d8     1         1         1       29s
```

## Verifying the Installation

```shell
❯ cmctl check api
The cert-manager API is ready
```

## Manual Verification

### Create an Issuer to test the webhook works okay.

```shell
cat <<EOF > test-resources.yaml
apiVersion: v1
kind: Namespace
metadata:
name: cert-manager-test
---
apiVersion: cert-manager.io/v1
kind: Issuer
metadata:
name: test-selfsigned
namespace: cert-manager-test
spec:
selfSigned: {}
---
apiVersion: cert-manager.io/v1
kind: Certificate
metadata:
name: selfsigned-cert
namespace: cert-manager-test
spec:
dnsNames:
    - example.com
secretName: selfsigned-cert-tls
issuerRef:
    name: test-selfsigned
EOF
```
### Create the test resources

```shell
kubectl apply -f test-resources.yaml
```


```shell
kubectl describe certificate -n cert-manager-test

Name:         selfsigned-cert
Namespace:    cert-manager-test
...
Spec:
Dns Names:
    example.com
Issuer Ref:
    Name:       test-selfsigned
Secret Name:  selfsigned-cert-tls
Status:
Conditions:
    Last Transition Time:  2023-01-23T22:27:06Z
    Message:               Certificate is up to date and has not expired
    Observed Generation:   1
    Reason:                Ready
    Status:                True
    Type:                  Ready
Not After:               2023-04-23T22:27:06Z
Not Before:              2023-01-23T22:27:06Z
Renewal Time:            2023-03-24T22:27:06Z
Revision:                1
Events:
Type    Reason     Age   From                                       Message
----    ------     ----  ----                                       -------
Normal  Issuing    44s   cert-manager-certificates-trigger          Issuing certificate as Secret does not exist
Normal  Generated  44s   cert-manager-certificates-key-manager      Stored new private key in temporary Secret resource "selfsigned-cert-kgtpb"
Normal  Requested  44s   cert-manager-certificates-request-manager  Created new CertificateRequest resource "selfsigned-cert-dmqqj"
Normal  Issuing    44s   cert-manager-certificates-issuing          The certificate has been successfully issued
```

Cleanup

```shell
kubectl delete -f test-resources.yaml
```