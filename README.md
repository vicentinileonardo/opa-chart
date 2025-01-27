# OPA chart

# WIP - not ready yet

This is a Helm chart for [Open Policy Agent (OPA)](https://www.openpolicyagent.org/docs/latest/kubernetes-tutorial/).

## How to install

### Prerequisites

Label kube-system namespace so that OPA does not control the resources in those namespaces:
```bash
kubectl label ns kube-system openpolicyagent.org/webhook=ignore
```

Communication between Kubernetes and OPA must be secured using TLS. 
To configure TLS, use `openssl` to create a certificate authority (CA) and certificate/key pair for OPA:
```bash
openssl genrsa -out ca.key 2048
openssl req -x509 -new -nodes -sha256 -key ca.key -days 100000 -out ca.crt -subj "/CN=admission_ca"
```

Generate the TLS key and certificate for OPA:
```bash
cat >server.conf <<EOF
[ req ]
prompt = no
req_extensions = v3_ext
distinguished_name = dn

[ dn ]
CN = opa.opa.svc

[ v3_ext ]
basicConstraints = CA:FALSE
keyUsage = nonRepudiation, digitalSignature, keyEncipherment
extendedKeyUsage = clientAuth, serverAuth
subjectAltName = DNS:opa.opa.svc,DNS:opa.opa.svc.cluster,DNS:opa.opa.svc.cluster.local
EOF
```

```bash
openssl genrsa -out server.key 2048
openssl req -new -key server.key -sha256 -out server.csr -extensions v3_ext -config server.conf
openssl x509 -req -in server.csr -sha256 -CA ca.crt -CAkey ca.key -CAcreateserial -out server.crt -days 100000 -extensions v3_ext -extfile server.conf
```

Create a Secret to store the TLS credentials for OPA:
```bash
kubectl create secret tls opa-server --cert=server.crt --key=server.key --namespace opa
```

### Chart installation

Install the chart:
```sh
helm repo add lv https://leonardovicentini.com/helm-charts/charts
helm repo update
helm install opa lv/opa -n opa
```
