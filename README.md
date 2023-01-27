# Cert Manager with Istio

This repository explains the Cert Manager installation and usage process with Istio Load Balancer.

The example used in this demo is from the sample application, bookinfo, that comes bundled with istio.

## Issuer

The first thing needs to configured after  installing cert-manager is an Issuer or a ClusterIssuer. These are resources that represent certificate authorities (CAs) able to sign certificates in response to certificate signing requests.

An **Issuer** is a namespaced resource, and it is not possible to issue certificates from an Issuer in a different namespace. This means you will need to create an Issuer in each namespace you wish to obtain Certificates in.

If you want to create a single Issuer that can be consumed in multiple namespaces, you should consider creating a **ClusterIssuer** resource. This is almost identical to the Issuer resource, however is non-namespaced so it can be used to issue Certificates across all namespaces. ClusterIssuer gets created in the cert-manager namespace.

## Certificates

cert-manager has the concept of Certificates that define a desired X.509 certificate which will be renewed and kept up to date. A Certificate is a namespaced resource that references an Issuer or ClusterIssuer that determine what will be honoring the certificate request.

When a Certificate is created, a corresponding CertificateRequest resource is created by cert-manager containing the encoded X.509 certificate request, Issuer reference, and other options based upon the specification of the Certificate resource.