# cert-manager-webhook-ionos
### Install webhook 

Add helm repo

`helm repo add cert-manager-webhook-ionos https://hmz-28.github.io/cert-manager-webhook-ionos/`

install helm chart

`helm install cert-manager-webhook-ionos cert-manager-webhook-ionos/cert-manager-webhook-ionos -n ibm-common-services`

add secret

```
apiVersion: v1
stringData:
  IONOS_PUBLIC_PREFIX: <your-public-key>
  IONOS_SECRET: <your-private-key>
kind: Secret
metadata:
  name: ionos-secret
  namespace: cert-manager
type: Opaque

```
add prod issuer

```
apiVersion: cert-manager.io/v1
kind: Issuer
metadata:
  name: letsencrypt-ionos-prod
spec:
  acme:
    # The ACME server URL
    server: https://acme-v02.api.letsencrypt.org/directory
    # Email address used for ACME registration
    email: <your-email-address>
    # Name of a secret used to store the ACME account private key
    privateKeySecretRef:
      name: letsencrypt-ionos-prod
    # Enable the dns01 challenge provider
    solvers:
      - dns01:
          webhook:
            groupName: acme.smartech.tn
            solverName: ionos
            config:
              apiUrl: https://api.hosting.ionos.com/dns/v1
              publicKeySecretRef:
                key: IONOS_PUBLIC_PREFIX
                name: ionos-secret
              secretKeySecretRef:
                key: IONOS_SECRET
                name: ionos-secret
