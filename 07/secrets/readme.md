# Secrets
are special type of resource that preserves confidential data.

## To create a `secret`
First, create certificate and private key using openssl
```bash
$ openssl genrsa -out https.key 2048
$ openssl req -new -x509 -key https.key -out https.cert -days 3650 -subj /CN=www.kubia-example.com
```
Then create a bs string file, and create a `secret` with that.
```bash
$ echo bar > foo
$ kubectl create secret generic fortune-https --from-file=https.key --from=file=https.cert --from-file=foo
```
Then the contents of the `secret` will be encoded into Base64-strings.