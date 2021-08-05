# Statefulsets: deploying replicated stateful applications
How do we make pods that persists their state data?

## Introducing `StatufulSets`  

`StatefulSets` are similar in many ways with `ReplicaSets`, although there are some differences like:  

- Pods having designated name
- When the pod is dead, the new pod takes the name 
- Pods have identity
- .. and more

So we should think the pods from `ReplicaSets` as cattles, whereas those from `StatefulSets` are like pet dogs.

## List
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

