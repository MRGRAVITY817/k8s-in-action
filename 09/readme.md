# Deployments: updating applications declaratively

In this chapter we will see how K8s deals with automatic pod updates.
There are several kind of pod updates:
- Deleting old pods and replacing them.
- Switching from the old to new version at once.
- Switching from the old to new, one by one(rolling update)

## Rolling update with ReplicaController (seems like deprecated now)
With `kubectl rolling-update` keyword, we can perform rolling update with rc.
```console
$ kubectl rolling-update kubia-v1 kubia-v2 --image=luksa/kubia:v2
```
