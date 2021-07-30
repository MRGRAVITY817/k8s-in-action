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

## Creating a `secret` for auth with Docker Registry
For that use, we use special type of `secret` called `docker-registry` (wow, no one could have guessed that!)
```bash
$ kubectl create secret docker-registry mydockerhubsecret \ 
  --docker-username=myusername --docker-password=mypassword --docker-email=my.email@provider.com
```
Then pull the image from private registry
```yaml
...
	spec:
		imagePullSecrets
		- name: mydockerhubsecret
		containers:
		- image: username/private:tag
			name: main
...
```