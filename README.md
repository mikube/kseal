<h1 align="center">☸:lock_with_ink_pen: kseal</h1>

A Shorthand for kubeseal. Works well with sealed-secrets and YAML-based template engines.


## Why kseal?
* Problem of sealed-secrets
    * sealed-secrets requires `kind: Secret` files for encryption and decryption. It doesn't work well with template engines such as helm or kustomize
        * We want to manage environment-specific values outside templates, within independent YAML files
    * It's hard to keep and manage consistent `namespace`s and `name`s with sealed-secrets-key
        * Especially, it's so hard to check or migrate existing sealed secrets
* Solution by kseal
    * Manage your encrypted secrets in YAML files like sops (by more trivial way)
        * Encryption and decryption with only YAML values files
        * Automatically embeds meta info for decryption when encryption
            * Decrypt without your memory!
        * And/Or can use an unified config file for both encryption and decryption


## Overview
Imagine GitOps-way manifest with a template engine...
```
/my-project
  /my-app
    /templates
      deployment.yaml
      ...
      sealedsecrets.yaml
    /values
      dev-values.yaml
      prd-values.yaml
```

```yaml
# sealedsecrets.yaml
apiVersion: bitnami.com/v1alpha1
kind: SealedSecret
...
spec:
  encryptedData:
  {{- range $i, $e := .Values.secrets }}
    {{ $e.name }}: {{ $e.value -}}
  {{ end }}
...
```

```yaml
# dev-values.yaml
secrets:
- name: db_password
  value: <encrypted_dev_db_password> # how to manage this field?
...
```

### Quick example
Before
```yaml
# dev-values.yaml
secrets:
  - name: db_password
    value: # Empty fields will be a candidate for being encrypted!
  - name: api_secret
    value: # Empty fields will be a candidate for being encrypted!
message: devlopment
secretMessage: # Empty fields will be a candidate for being encrypted!
```

```console
$ kseal enc -f dev-values.yaml -q '.secrets[].value' -q .secretMessage -c dev
Enter secret for dev-values.yaml (.secrets.0.value) or empty for skipping: <input secret><enter>
Enter secret for dev-values.yaml (.secrets.1.value) or empty for skipping: <input secret><enter>
Enter secret for dev-values.yaml (.secretMessage) or empty for skipping: <enter> # skip
```

After
```yaml
# dev-values.yaml
secrets:
  - name: db_password
    value: AgAjtavZJh... # [sealed] {scope: strict, namespace: my-namespace, name: my-secret, context: dev}
  - name: api_secret
    value: AgCqxB/PsR... # [sealed] {scope: strict, namespace: my-namespace, name: my-secret, context: dev}
message: devlopment
secretMessage: AgDo1s9ZnX... # [sealed] {scope: strict, namespace: my-namespace, name: my-secret, context: dev}
```

You can check contents easily
```console
$ kseal dec -e -f dev-values.yaml -P
Successfully decrypted dev-values.yaml (secrets.0.value) by scope=, namespace=my-namespace, name=my-secret, context=dev, sealed-secret-key=[sealed-secrets-keygvqgl]: my-password-for-dev-db
Successfully decrypted dev-values.yaml (secrets.1.value) by scope=, namespace=my-namespace, name=my-secret, context=dev, sealed-secret-key=[sealed-secrets-keygvqgl]: my-dev-api-secret
Successfully decrypted dev-values.yaml (secretMessage) by scope=, namespace=my-namespace, name=my-secret, context=dev, sealed-secret-key=[sealed-secrets-keygvqgl]: This is a secret message
```

### Use an unified config
Prepare an encryption config
```yaml
# .kseal.yaml
- valuesFiles:
  - dev-values.yaml
  queries:
  - .secrets[].value
  - .secretMessage
  scope: strict
  namespace: my-namespace
  name: my-secret
  context: dev
```

Just use it
```console
$ kseal enc -C .kseal.yaml
Enter secret for dev-values.yaml (.secrets.0.value) or empty for skipping: <input secret><enter>
Enter secret for dev-values.yaml (.secrets.1.value) or empty for skipping: <input secret><enter>
Enter secret for dev-values.yaml (.secretMessage) or empty for skipping: <input secret><enter>
```


## Prerequisites
* [mikefarah/yq](https://github.com/mikefarah/yq)
* [kubeseal](https://github.com/bitnami-labs/sealed-secrets)
* kubectl


## :soon: Usage
### :inbox_tray: Installation
```sh
curl -sSL https://raw.githubusercontent.com/mikube/kseal/master/kseal > kseal
chmod +x kseal
# mv kseal into your $PATH
```

### :lock: Encryption:
* Select encryption targets
    * By a config file (See Config format section or examples for details)
        * `kseal enc -C .kseal.yaml`
    * By arguments
        * `kseal enc -f values1.yaml -f values2.yaml -q '.secrets[]' -q '.otherSecrets[].value' -n my-namespace -N my-secret`
* How to encrypt
    * By a local key
        * (Not implemented)
    * By a remote key
        * Can change remote by context
### :unlock: Decryption
* Select decryption targets
    * By a config file (See Config format section or examples for details)
        * `kseal dec -C .kseal.yaml`
    * By arguments
        * With embedded comments
            * `kseal dec -e -f values3.yaml`
        * Without embedded comments
            * `kseal dec -f values3.yaml -q '.otherSecrets[].value' -n my-namespace -N my-secret`
* How to decrypt
    * By a local private key
        * `kseal dec -e -f values3.yaml -p private-key.yaml`
    * By a specified remote key
        * `kseal dec -e -f values3.yaml -S sealed-secrets-key`
            * Can change remote by context
    * By any one of remote keys (brute force by `sealedsecrets.bitnami.com/sealed-secrets-key` label)
        * `kseal dec -e -f values3.yaml`
            * Can change remote by context


## :triangular_flag_on_post: Subcommands & Flags
```
Subcommands:
  e, enc, encrypt             Encryption mode
  d, dec, decrypt             Decryption mode
Flags:
  -h, --help                  Print usage
  -v, --verbose               Enable verbose logs
  -c, --context               kubeconfig context
      --controller-name       sealed-secrets-controller name

  -C, --config                Path to a config file
  -f, --values                Target values file (Can be multiple)
  -q, --query                 YAML query for secrets (Can be multiple). See https://mikefarah.gitbook.io/yq/
  -s, --scope                 sealed-secret scope (Default: strict). See https://github.com/bitnami-labs/sealed-secrets#scopes
  -n, --namespace             Namespace for secret
  -N, --name                  Secret name
  -e, --extract-emb           [For Decryption] Extract scope, namespace, name, etc. from embedded comments

  -P, --print-secrets         Print decrypted or before-encrypting secrets
      --sealed-marker         Marker text for embedding meta info of encryption (Default: [sealed])
      --infer-from            [For Encryption] Path to a secret file which will be used for inferring scope, namespace, and name
      --disable-emb           [For Encryption] Disable embedded comments when encryption
  -u, --update                [For Encryption] Update even if already encrypted.
                              Decryption for updating will work based on embedded comment.
                              If want to use different keys between encryption and decryption,
                              specify optional key for decryption with the following flags.
                              * --private-key-for-decryption-while-updating
                              * --sealed-secrets-key-for-decryption-while-updating

  -p, --private-key           [For Decryption] Private key for decryption
  -S, --sealed-secrets-key    [For Decryption] Secret sealed-secrets-key name for decryption

      --yq                    Path to yq (Default: yq in \$PATH)
                              Expects https://github.com/mikefarah/yq
      --kubeseal              Path to kubeseal (Default: kubeseal in \$PATH)
      --kubectl               Path to kubectl (Default: kubectl in \$PATH)
```


## :wrench: Config format
```yaml
- valuesFiles: # YAML values file to hold encrypted secrets
  - dev-values1.yaml # can specify multiple values files
  - dev-values2.yaml
  context: dev # kubeconfig context
  queries: # fields you want to encrypt
  - .secrets[].value # can specify multiple YAML queries (yq)
  - .secretMessage
  scope: strict # sealed-secrets scope
  namespace: my-dev-namespace # namespace for secret
  name: my-dev-secret # name of secret
  controllerName: sealed-secrets # sealed-secrets controller's name

# multiple configs in a file
- valuesFiles:
  - prd-values1.yaml
  - prd-values2.yaml
  context: prd
  queries:
  - .secrets[].value
  - .secretMessage
  scope: strict
  namespace: my-prd-namespace
  name: my-prd-secret
  controllerName: sealed-secrets
```


## Examples
See [examples](./examples).
