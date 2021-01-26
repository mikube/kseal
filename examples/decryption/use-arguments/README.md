# use-arguments
```console
$ # Decrypt by embedded info comments
$ kseal d -e -f prd-values.yaml
Successfully decrypted prd-values.yaml (.secrets.0.value) by scope=strict, namespace=my-namespace, name=my-secret, context=:kind-kind, sealed-secret-key=[sealed-secrets-keygvqgl]
Successfully decrypted prd-values.yaml (.secrets.1.value) by scope=strict, namespace=my-namespace, name=my-secret, context=:kind-kind, sealed-secret-key=[sealed-secrets-keygvqgl]
Successfully decrypted prd-values.yaml (.secretMessage) by scope=strict, namespace=my-namespace, name=my-secret, context=:kind-kind, sealed-secret-key=[sealed-secrets-keygvqgl]


$ # Decrypt specified fields using automatically retrieved sealed-secrets-key
$ kseal d -f dev-values.yaml -f prd-values.yaml -q '.secretMessage' --context kind-kind -n my-namespace -N my-secret
Successfully decrypted dev-values.yaml (secretMessage) by scope=, namespace=my-namespace, name=my-secret, context=kind-kind:kind-kind, sealed-secret-key=[sealed-secrets-keygvqgl]
Successfully decrypted prd-values.yaml (secretMessage) by scope=, namespace=my-namespace, name=my-secret, context=kind-kind:kind-kind, sealed-secret-key=[sealed-secrets-keygvqgl]


$ # Decrypt specified fields using specified sealed-secrets-key and print secrets by (`-P`)
$ kseal d -P -f dev-values.yaml -f prd-values.yaml -q '.secretMessage' --context kind-kind -n my-namespace -N my-secret -S sealed-secrets-keygvqgl
Successfully decrypted dev-values.yaml (secretMessage) by scope=, namespace=my-namespace, name=my-secret, context=kind-kind:kind-kind, sealed-secret-key=[sealed-secrets-keygvqgl]: This is a secret message
Successfully decrypted prd-values.yaml (secretMessage) by scope=, namespace=my-namespace, name=my-secret, context=kind-kind:kind-kind, sealed-secret-key=[sealed-secrets-keygvqgl]: This is a secret message !!!


$ # Decrypt by specified fields using a local private key and print secrets by (`-P`)
$ kseal d -P -f dev-values.yaml -f prd-values.yaml -q '.secrets[].value' --context kind-kind -n my-namespace -N my-secret -p ../../example-key.yaml
Successfully decrypted dev-values.yaml (secrets.0.value) by scope=, namespace=my-namespace, name=my-secret, context=kind-kind:kind-kind, private-key=example-private-key.yaml: my-password-for-dev-db
Successfully decrypted dev-values.yaml (secrets.1.value) by scope=, namespace=my-namespace, name=my-secret, context=kind-kind:kind-kind, private-key=example-private-key.yaml: my-dev-api-secret
Successfully decrypted prd-values.yaml (secrets.0.value) by scope=, namespace=my-namespace, name=my-secret, context=kind-kind:kind-kind, private-key=example-private-key.yaml: my-password-for-prd-db
Successfully decrypted prd-values.yaml (secrets.1.value) by scope=, namespace=my-namespace, name=my-secret, context=kind-kind:kind-kind, private-key=example-private-key.yaml: my-prd-api-secret
```
