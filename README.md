# Ansible Vault GPG Client

A common requirement is to have different vaults with different passwords for different environments.

There are existing tools to do that with
[pass / gopass](https://github.com/me-vlad/ansible-vault-pass-client),
[StoredSafe](https://github.com/storedsafe/vault-storedsafe-client),
[BitWarden](https://theorangeone.net/posts/ansible-vault-bitwarden) and probably others.

However, there was nothing convenient to use for just GPG... until now!

## Usage

First, create a `vault` subdirectory in your Ansible project and put the `vault-gpg-client` script there:

```shell
$ cd my-ansible-project
$ mkdir vault
$ wget -O vault/vault-gpg-client "https://raw.githubusercontent.com/JonasGroeger/ansible-vault-gpg-client/main/vault-gpg-client"
/projects/my-ansible-repo
$ chmod +x vault/vault-gpg-client
```

It should look something like this:

```shell
$ tree
.
├── ansible.cfg
└── vault
    └── vault-gpg-client
```

Then, configure the location of the GPG encrypted password files in the `ansible.cfg` file.
We're using the `vault/` directory aswell:

```
[vault]
gpg = vault/
```

Next, create a file called `vault/$VAULT_ID.asc` where the `VAULT_ID` is the name / label of the vault.
If you differentiate between environments, this might be called `vault/dev.asc` and `vault/prod.asc`.
The fingerprints can (of course) be different between `dev` and `prod`.

```shell
echo -n "vault-password-dev"  | gpg --encrypt --armor --recipient YOUR_GPG_FINGERPRINT > vault/dev.asc
echo -n "vault-password-prod" | gpg --encrypt --armor --recipient YOUR_GPG_FINGERPRINT > vault/prod.asc
```


As a final step, lets encrypt i.e. a variable with this setup:

```shell
ansible-vault encrypt_string --vault-id=dev@vault/vault-gpg-client "secret-value" --name "password"
```

This will decrypt the `vault/dev.asc` file and use it as the vault password. Cool, right :)

# Contributing

If you have any issues of want to contribute, feel free to create an issue or a pull request.
If you create an issue, be sure to set `VAULT_GPG_CLIENT_DEBUG` to any value and include the output

# Attribution

This work is inspired by the [ansible-vault-gopass-client](https://github.com/me-vlad/ansible-vault-pass-client).
