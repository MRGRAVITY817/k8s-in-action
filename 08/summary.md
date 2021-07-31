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

## How to explore cluster metadata?
### `kubectl proxy`
Of course you can access metadata from actual https://cluster.api, but it can be cumbersome since we need to define -k variable or ca certs for `https`. Yet we have better solution. 
```bash
$ kubectl proxy
# starting to serve at http://localhost:8001
```
this will start local proxy server that you can curl in.

### Explore with endpoints
When proxy server has started, try various endpoints to explore various metadata.
```bash
$ curl http://localhost:8001/apis/batch
$ curl http://localhost:8001/apis/batch/v1
$ curl http://localhost:8001/apis/batch/v1/jobs
...
```

### How to get cluster info from pod?
When you look inside the pod filesystem, you'll find ca.crt, namespace, and token inside /var/run/secrets/kubernetes.io/serviceaccount. Those are accommodated for every pods.
Use curl command with extra options to get K8 api.
```console
# export CURL_CA_BUNDLE=/var/run/secrets/kubernetes.io/serivceaccount/ca.crt
# TOKEN=$(/var/run/secrets/kubernetes.io/serivceaccount/token)
# NS=$(/var/run/secrets/kubernetes.io/serivceaccount/namespace)
# curl -H "Authorization: Bearer $TOKEN" https://kubernetes/api/v1/namepaces/$NS/pods
...
```
But this can be very difficult. Use ambassador instead.

### Using ambassador to get cluster data from pod(recommended!).
By extra container called `ambassador`, our life will get easier :D
```yaml
...
- name: ambassador
  image: luksa/kubectl-proxy:1.6.2
...
```
```console
$ kubectl exec -it <podname> -c main bash
# curl localhost:8001
...
```

## Use client libraries to talk with API server
When you plan to use more functionalities with api server, try exploring language specific k8 client libraries! 
- Golang client—https://github.com/kubernetes/client-go
- Python—https://github.com/kubernetes-incubator/client-python
- Java client by Fabric8—https://github.com/fabric8io/kubernetes-client
- Java client by Amdatu—https://bitbucket.org/amdatulabs/amdatu-kubernetes
- Node.js client by tenxcloud—https://github.com/tenxcloud/node-kubernetes-client 
- Node.js client by GoDaddy—https://github.com/godaddy/kubernetes-client
- PHP—https://github.com/devstub/kubernetes-api-php-client
- Another PHP client—https://github.com/maclof/kubernetes-client