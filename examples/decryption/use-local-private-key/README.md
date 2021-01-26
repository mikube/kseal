# use-local-private-key
```console
$ kseal d -e -C .kseal.yaml -d -P -p ../../example-key.yaml
Successfully decrypted dev-values.yaml (secrets.0.value) by scope=strict, namespace=my-namespace, name=my-secret, context=kind-kind:kind-kind, private-key=/home/amaya/work/mikube/helm-sealed-secrets/examples/decryption/example-private-key.yaml: my-password-for-dev-db
Successfully decrypted dev-values.yaml (secrets.1.value) by scope=strict, namespace=my-namespace, name=my-secret, context=kind-kind:kind-kind, private-key=/home/amaya/work/mikube/helm-sealed-secrets/examples/decryption/example-private-key.yaml: my-dev-api-secret
Successfully decrypted dev-values.yaml (secretMessage) by scope=strict, namespace=my-namespace, name=my-secret, context=kind-kind:kind-kind, private-key=/home/amaya/work/mikube/helm-sealed-secrets/examples/decryption/example-private-key.yaml: This is a secret message
Successfully decrypted prd-values.yaml (secrets.0.value) by scope=strict, namespace=my-namespace, name=my-secret, context=kind-kind:kind-kind, private-key=/home/amaya/work/mikube/helm-sealed-secrets/examples/decryption/example-private-key.yaml: my-password-for-prd-db
Successfully decrypted prd-values.yaml (secrets.1.value) by scope=strict, namespace=my-namespace, name=my-secret, context=kind-kind:kind-kind, private-key=/home/amaya/work/mikube/helm-sealed-secrets/examples/decryption/example-private-key.yaml: my-prd-api-secret
Successfully decrypted prd-values.yaml (secretMessage) by scope=strict, namespace=my-namespace, name=my-secret, context=kind-kind:kind-kind, private-key=/home/amaya/work/mikube/helm-sealed-secrets/examples/decryption/example-private-key.yaml: This is a secret message !!!
```
