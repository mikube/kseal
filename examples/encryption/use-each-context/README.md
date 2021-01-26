# use-each-context
```console
$ kseal e -C .kseal.yaml
Enter secret for dev-values.yaml (.secrets.0.value) or empty for skipping: <input secret><enter>
Enter secret for dev-values.yaml (.secrets.1.value) or empty for skipping: <input secret><enter>
Enter secret for dev-values.yaml (.secretMessage) or empty for skipping: <input secret><enter>
Enter secret for prd-values.yaml (.secrets.0.value) or empty for skipping: <enter> # skip
Enter secret for prd-values.yaml (.secrets.1.value) or empty for skipping: <input secret><enter>
Enter secret for prd-values.yaml (.secretMessage) or empty for skipping: <input secret><enter>
```

## Notes
* To run this example, need k8s/sealed-secrets-controller with proper context name.
