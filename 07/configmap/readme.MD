# Config Map
ConfigMap is a key:value map that can store configuration info of containers.

## Creating a `ConfigMap` 
### From command
1. Using `--from-literal` option
```bash
$ kubectl create configmap myconfigmap \
          --from-literal=foo=bar  \
          --from-literal=bar=barz
```

2. From `.yaml` file 
```bash
$ kubectl create -f fortune-config.yaml
```

3. From config file
- Without `customkey` option, configmap will save config-file.conf contents with key of it's filename.
```bash
$ kubectl create configmap my-config \
          --from-file=customkey=config-file.conf
```

4. ... and combining all those options
```bash
$ kubectl create configmap my-config
          --from-file=foo.json \
          --from-file=bar=foobar.conf \
          --from-file=config-opts/ \
          --from-file=foo.json \
```


