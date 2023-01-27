# Create an Cluster Issuer

## Create an issuer Request
    kubectl apply -f configurations/ClusterIssuer.yaml

## Get the status of the Issuer

    kubectl describe clusterissuer letsencrypt -n cert-manager

    ...
    Spec:
    Acme:
        Email:            farooqnafey@gmail.com
        Preferred Chain:  
        Private Key Secret Ref:
        Name:  letsencrypt-account-key
        Server:  https://acme-v02.api.letsencrypt.org/directory
        Solvers:
        http01:
            Ingress:
            Class:  istio
    Status:
    Acme:
        Last Registered Email:  farooqnafey@gmail.com
        Uri:                    https://acme-v02.api.letsencrypt.org/acme/acct/935418427
    Conditions:
        Last Transition Time:  2023-01-27T01:59:03Z
        Message:               The ACME account was registered with the ACME server
        Observed Generation:   1
        Reason:                ACMEAccountRegistered
        Status:                True
        Type:                  Ready
    Events:                    <none>

## Check the Secret

A secret with a private key is created

    ❯ kubectl describe secret -n cert-manager letsencrypt-account-key
    Name:         letsencrypt-account-key
    Namespace:    cert-manager
    Labels:       <none>
    Annotations:  <none>

    Type:  Opaque

    Data
    ====
    tls.key:  1675 bytes


### Links
[Cluster Issuer](https://cert-manager.io/docs/configuration/acme/)
