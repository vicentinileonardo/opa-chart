In the current configuration, OPA is NOT running with an authorization policy configured. This means that clients can read and write policies in OPA. If you are deploying OPA in an insecure environment, be sure to configure authentication and authorization on the daemon. See the Security page for details: https://www.openpolicyagent.org/docs/security.html.

If you want to use a private registry for OPA policies, you can set the `services.dockerhub-registry.credentials.bearer.token` value in the args of the opa container to the path of a file containing the token.
`dockerhub-registry` is the name of the registry service in the `values.yaml` file.
The token should be a Bearer token.

```yaml
# set token for OCI registry, if needed
# - "--set=services.dockerhub-registry.credentials.bearer.scheme=Bearer"
# - "--set=services.dockerhub-registry.credentials.bearer.token=/path/to/token"
```


## RBAC

kubectl auth can-i --as=system:serviceaccount:opa:default --namespace=opa list vmtemplates.composition.krateo.io
yes
kubectl auth can-i --as=system:serviceaccount:opa:default --namespace=opa watch vmtemplates.composition.krateo.io
yes
kubectl auth can-i --as=system:serviceaccount:opa:default --namespace=opa get vmtemplates.composition.krateo.io
yes

kubectl auth can-i --as=system:serviceaccount:opa:default --namespace=opa update configmaps
yes
kubectl auth can-i --as=system:serviceaccount:opa:default --namespace=opa patch configmaps
yes