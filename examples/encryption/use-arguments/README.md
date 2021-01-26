# use-arguments
```console
$ kseal e -f prd-values.yaml -q '.secretMessage' --context prd \
$   --namespace my-namespace --name my-secret
Enter secret for prd-values.yaml (.secretMessage) or empty for skipping: <input secret><enter>


$ # Encrypt multiple files' common fileds at once
$ kseal e -f dev-values.yaml -f dev-values2.yaml -q '.secrets[].value' --context dev \
$   --namespace my-namespace --name my-secret
Enter secret for dev-values.yaml (.secrets.0.value) or empty for skipping: <input secret><enter>
Enter secret for dev-values.yaml (.secrets.1.value) or empty for skipping: <input secret><enter>


$ # Update already encrypted fields
$ # Overwrite by a new secret
$ kseal e -u -f dev-values.yaml -q '.secretMessage' --context prd \
$   --namespace my-namespace --name my-secret
Enter secret for dev-values.yaml (.secretMessage) or empty for attempting to use existing secret: <input new secret><enter>


$ # Update already encrypted fields
$ # Re-encrypt by the same namespace, name, and key
$ kseal e -u -f dev-values.yaml -q '.secretMessage' --context prd \
$   --namespace my-namespace --name my-secret
Enter secret for dev-values.yaml (.secretMessage) or empty for attempting to use existing secret: <enter>
Successfully decrypted dev-values.yaml (.secretMessage) by scope=strict, namespace=my-new-namespace, name=my-new-secret, context=:, private-key=../../example-key.yaml


$ # Update already encrypted fields
$ # Re-encrypt by different namespaces, names, and keys
$ kseal e -u -f dev-values.yaml -q '.secretMessage' --context prd \
$   --namespace my-new-namespace --name my-new-secret --private-key-for-decryption-while-updating=../../example-key.yaml
Enter secret for dev-values.yaml (.secretMessage) or empty for attempting to use existing secret: <enter>
Successfully decrypted dev-values.yaml (.secretMessage) by scope=strict, namespace=my-new-namespace, name=my-new-secret, context=:, private-key=../../example-key.yaml
```

## Notes
* This example contains some already encrypted fields. By default, kseal will skip them. If you want to update them, use `-u (--update)` flag.
* To run this example, need k8s/sealed-secrets-controller with proper context name.
