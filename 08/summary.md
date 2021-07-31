# Accesssing pod metadata and other resources from applications

## By using downward api, we can get the following metadata,
- the pod's name
- the pod's ip address
- the namespace the pod belongs to
- the name of the node the pod is running on
- the name of the service account the pod is running under
- the CPU and memory requests for each container
- the CPU and memory limits for each container
- the pod's label
- the pod's annotations

### Exposing meta data through environment variables
For normal metadata, you use `fieldRef` prop, for resource related you can use `resourceFieldRef` props to read them.
```yaml
...
  containers
	- name: POD_NAME
	  valueFrom:
		  fieldRef: 
			  fieldPath: metadata.name
...
  - name: CONTAINER_CPU_REQUEST_MILLICORES
	  valueFrom:
		  resourceFieldRef:
			  resource: requests.cpu
				# this divisor will send the amount of cpu size
				# divided by 1millicore, or one thousandths.
				divisor: 1m 
...
```
You can find out pod's env var with following command
```bash
$ kubectl exec downward -- env
```

### Passing metadata through files with volume
Since metadata like labels or annotations cannot be passed through env var, we use volumes to mount files that refer those info.
```yaml
...
spec:
  containers: 
...
		volumeMounts: 
		- name: downward
		  mountPath: /etc/downward
	volumes: 
	- name: downward
	  downwardAPI:
...
		- path: "labels"
		  fieldRef:
			  fieldPath: metadata.labels
		- path: "annotations"
		  fieldRef:
			  fieldPath: metadata.annotations
...
```
Because pods can have multiple containers, don't forget to define container name for resource data.
```yaml
...
		- path: "containerCpuRequestMillicores"
		  resourceFieldRef:
			  containerName: main
				resource: requests.cpu
				divisor: 1m
...
```
Since using downward api is easy but limited, it should be used for reffering to comparably simple metatdata.