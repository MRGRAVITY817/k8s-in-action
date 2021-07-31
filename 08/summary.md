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


