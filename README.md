# Kubernetes basic


### What is Kubernetes?
### What are pods

NPC node pods container
Nodes->Pods->container

# Goal of this task
- Implement Deployment
- Delete some stuff!
- Update the container image Via Deployment

### My initial state
Output:

![Screenshot from 2023-06-06 11-33-49](https://github.com/aadilraza339/k8s-aws-eks-fargate-and-devops/assets/47937273/93b888d5-6948-4aa8-8c20-844f50a491b7)

Lets create deployment

To create deployment we need to run our this [file](https://github.com/aadilraza339/k8s-aws-eks-fargate-and-devops/blob/main/nginx-deployment-withrolling.yaml) for deployment.
Output:
![image](https://github.com/aadilraza339/k8s-aws-eks-fargate-and-devops/assets/47937273/ed4a7f0a-c026-4391-bfa7-2a2fdd8ac295)

Now lets check our create pods with help of this command:

```
    kubectl get all


```
Output

![image](https://github.com/aadilraza339/k8s-aws-eks-fargate-and-devops/assets/47937273/2d642d74-ec6e-435a-bdda-0d214f212493)

So now let's delete one of the pods. And then this replica set has that three desired part and three current pods.
As soon as I delete one of the pods, the replicas that will see that the desired pod is three, but the current part is two.
So it is going to spring up on their part.

I am going to delete this pod with the `specific` name.
**Delete command**

```
   kubectl delete pod <pod-name> | kubectl delete pod testdeploy-5f57f78888-6xxrn
```
Output
![image](https://github.com/aadilraza339/k8s-aws-eks-fargate-and-devops/assets/47937273/f122f40d-f661-4af6-9d2c-bca3667e2580)


As you can see I deleted this name of pods `testdeploy-5f57f78888-6xxrn` and in the output you can see this pod replaced with new one.

### Let's delete replica now

Now we are going to go one level higher, right? Remember, replica said restarts, pod, deployment's restored, replica set.
Now we are going to delete this replica set. OK, so let's delete this replica set.

### Get replica command:
```
   kubectl get rs
```
Now delete the replica
`Command`

```
   kubectl delete rs testdeploy-5f57f78888
```

Ouput:
![image](https://github.com/aadilraza339/k8s-aws-eks-fargate-and-devops/assets/47937273/e90e7896-3ae0-4c18-a14d-7eb49af6c17f)









