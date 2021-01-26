# use-common-context
```console
$ # Encrypt by a config file
$ # -v option for showing additional info (e.g., already encrypted fileds)
$ kseal e -C .kseal.yaml -v
Enter secret for dev-values.yaml (.secrets.0.value) or empty for skipping: <input secret><enter>
Enter secret for dev-values.yaml (.secrets.1.value) or empty for skipping: <input secret><enter>
Probably already encrypted dev-values.yaml (.secretMessage): AgDo1s9ZnX...
Probably already encrypted dev-values.yaml (.secrets.0.value): AgDDXV+gjK...
Enter secret for prd-values.yaml (.secrets.1.value) or empty for skipping: <enter> # skip
Enter secret for prd-values.yaml (.secretMessage) or empty for skipping:<input secret><enter>
```

## Notes
* This example contains some already encrypted fields. By default, kseal will skip them. If you want to update them, use `-u (--update)` flag.
* To run this example, need k8s/sealed-secrets-controller with proper context name.
