# Config Map
ConfigMap is a key:value map that can store configuration info of containers.

## Creating a `ConfigMap` 
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

## Passing a ConfigMap entry to a container
1. We can pass configmap entry using `valueFrom` field.
```yaml
...
      env:
        - name: INTERVAL
          valueFrom:
            # 'configMapKeyRef' will get configMap data
            # that matches with the given key
            configMapKeyRef:
              name: fortune-config
              key: sleep-interval
...
```

2. We can also pass the entire env vars in configmap using `envForm`.
```yaml
# assume we have "FOO" "BAR" "FOO-BAR" in my-config-map
...
      spec:
        containers:
        - image: some-image
          envFrom: 
          # we will get only "CONFIG_FOO" and "CONFIG_BAR",
          # but not "CONFIG_FOO-BAR" since the prefix key word
          # will ignore the hyphen.
          - prefix: CONFIG_
            configMapRef: 
              name: my-config-map
...
```
