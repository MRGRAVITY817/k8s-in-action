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
### Problems?
This method is not so good, because it modifies our object directly.

## Using Deployments for updating apps declaratively
Create deployment with --record to get records of command revision history.
```console
$ kubectl create -f  kubia-deployment-v1.yaml --record
```
Check the status of deployment with `rollout`
```console
$ kubectl rollout status deployment kubia
```
You can also check that deployment automatically created `ReplicaSet`.
```console
$ kubectl get rs

NAME               DESIRED   CURRENT   READY   AGE
kubia-74967b5695   3         3         3       3m
```

## Updating object

### Use `patch` keyword to update object info.
```console
$ kubectl patch deployment kubia -p '{"spec": {"minReadySeconds": 10}}'
```
it's useful for modifying a single property without opening in a text editor.

### Use `set image` to update deployment image.
This will update image.
```console
$ kubectl set image deployment kubia nodejs=luksa/kubia:v2
$ kubectl get po 

NAME                     READY   STATUS        RESTARTS   AGE
kubia-74967b5695-2nmts   1/1     Terminating   0          14m
kubia-74967b5695-h45v9   1/1     Terminating   0          14m
kubia-74967b5695-zccc4   1/1     Terminating   0          14m
kubia-bcf9bb974-7mdp6    1/1     Running       0          6s
kubia-bcf9bb974-sjrph    1/1     Running       0          9s
kubia-bcf9bb974-vfncz    1/1     Running       0          18s
```
### Other options for updating object
- kubectl edit: Opens default editor to patch object 
- kubectl apply: Modifies the object with given yaml file, it needs the full description of a resource
- kubectl replace: Replaces the object, and needs actual object prehandedly

## Undoing rollout(version down)
If the current version is having trouble, we should version down.
```console
$ kubectl rollout undo deployment kubia
```
You can see the rollout history with `history` keyword
```console
$ kubectl rollout history deployment kubia

deployment.apps/kubia 
REVISION  CHANGE-CAUSE
2         kubectl create --filename=kubia-deployment-v1.yaml --record=true
3         kubectl create --filename=kubia-deployment-v1.yaml --record=true
```
You can see the `REVISION` in console result, which can be used as version history number that we can go back with `--to-revision` keyword.
```console
$ kubectl rollout undo deployment kubia --to-revision=1
```

## Controlling rate of rollout 
We can specify the number of pods tolerated to be when rollout with `maxSurge` and `maxUnavailable`.
```yaml
...
  spec:
	  strategy:
		  # this will tolerate 2~4 pods when updating deployment
		  rollingUpdate:
			  maxSurge: 1 # or by using percent
			  maxUnavailable: 1 # or by using percent
...
```