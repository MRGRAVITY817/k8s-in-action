# Statefulsets: deploying replicated stateful applications
How do we make pods that persists their state data?

## Introducing `StatufulSets`  

`StatefulSets` are similar in many ways with `ReplicaSets`, although there are some differences like:  

- Pods having designated name
- When the pod is dead, the new pod takes the name 
- Pods have identity
- .. and more

So we should think the pods from `ReplicaSets` as cattles, whereas those from `StatefulSets` are like pet dogs.

## Creating `StatefulSets`
### List
We can list the resource definition with `kind: List`.
```yaml
kind: List
apiVersion: v1
items:
- apiVersion: v1
...
- apiVersion: v1
...
- apiVersion: v1
...
```
### How to define `StatefulSets`
Defining statefulSet is very similar to replicaSet.
```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: kubia
...
  volumeClaimTemplates:
    - metadata:
        name: data
      spec:
			...

```
### Connect from headless service to pods
Make proxy(to avoid cumbersome ssl commands) and connect to Api endpoint
```console
$ kubectl proxy
Starting to serve on 127.0.0.1:8001 

$ curl localhost:8001/api/v1/namespaces/default/pods/kubia-0/proxy
You've hit kubia-0
Data stored on this pod: No data posted yet
```
### Connect from public service to pods
```console
$ curl localhost:8001/api/v1/namespaces/default/services/kubia-public/proxy/
You've hit kubia-1
Data stored on this pod: No data posted yet
```

## How to update `StatefulSets`?
```console
$ kubectl edit statefulset kubia
<this will open text editor>
```

## Pods disconnected cannot be reached
When the node is disconnected, then the control plane in that node will kill the pods. So no need for hurry to kill them by yourself.
