# Deployments: updating applications declaratively

In this chapter we will see how K8s deals with automatic pod updates.
There are several kind of pod updates:
- Deleting old pods and replacing them.
- Switching from the old to new version at once.
- Switching from the old to new, one by one(rolling update)

