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

Let move forward
Now I am going to update the conatainer image version from `image: nginx:1.16` to `image: nginx:1.17`.

For the better understanding see this image below.
![image](https://github.com/aadilraza339/k8s-aws-eks-fargate-and-devops/assets/47937273/c5c0a6fe-1106-47dc-8dfe-e380fd46be8e)

### Let rollupdate
Make the change `image: nginx:1.16` to `image: nginx:1.17` in file `nginx-deployment-withrolling.yaml`

Let run now
Command:

```
kubectl apply -f nginx-deployment-withrolling.yaml
```

![image](https://github.com/aadilraza339/k8s-aws-eks-fargate-and-devops/assets/47937273/370b6a98-f7bf-4ff4-aa5a-ed52a77c9eb2)

So you have this new replica set, which has all the three pods and the old one has no pods.

Remember before we tried to delete a replica set and it came back instantly. But now if I delete the old replica set with no pods running underneath it, should get deleted.

So delete `old replica`
```
kubectl delete rs testdeploy-5f57f78888
```
Old replica got deleted
Output

![image](https://github.com/aadilraza339/k8s-aws-eks-fargate-and-devops/assets/47937273/c5e351e4-1072-45c2-80ff-7b1aa2125db7)

The deployment doesn't bring it back because it doesn't manage anything.


### Now let's talk about services
Let's start by looking at a life of a simple part.
![image](https://github.com/aadilraza339/k8s-aws-eks-fargate-and-devops/assets/47937273/a0f7d82a-70be-4847-a9a3-bdeb51d38332)
At this point, we know how to deploy pods with specific content and images. </br>
**so let's say we have two nodes.**

Both nodes have separate IP addresses and each of these pods has its own unique IP address.
Pods within the same cluster can communicate with each other.
One way for the web server to communicate with these pods is by directly using their IP addresses.
However,  if a pod goes down or crashes, the replica set will kick in if you have deployed with two replicas.
It will bring up another pod with the same content or image, but the IP address of this new pod will be different from the one that died.
So, if the Nginx pod was using the IP address to connect to the MySQL pod, it will encounter issues because it won't be able to discover or reach the new pod.
Now, let's consider a scenario where the entire node fails, not just a single pod.
If the connections to this node fail, all the pods within it will also stop working.
When another node comes up, such as an Amazon instance, it will have a new IP address and new pods with different IP addresses.
In this case, the Nginx pods won't know how to discover or reach the new pods.
Another situation to consider is when these two pods are managed by a horizontal parlato scalar
and it as a system that adds more pods to the node as traffic increases, and removes pods when traffic decreases.
However, let's say this website pod somehow remains active while other pods go down.
**What happens then?**

Ideally, this pod should also direct traffic to the new pods, but it doesn't know how to find them because the Nginx pod was using the IP address of the previous pods.
All of these problems can be solved by using a service
### What is service?




































