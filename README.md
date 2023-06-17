# Section 1
### Goal of this task
- Implement Deployment
- Delete some stuff!
- Update the container image Via Deployment


### Prerequisites
- Clone `k8s-aws-eks-fargate-and-devops` [repository](https://github.com/aadilraza339/k8s-aws-eks-fargate-and-devops.git)
- Open your `Docker-Desktop` performance steps locally, if you don't have you can install them from this [link](https://github.com/aadilraza339/argocd-webapp#setting-up-pequiresties)

### My initial state

Will display information about all pods, services, deployments, and other resources in the current namespace.
```
kubectl get all
``` 
![Screenshot from 2023-06-06 11-33-49](https://github.com/aadilraza339/k8s-aws-eks-fargate-and-devops/assets/47937273/93b888d5-6948-4aa8-8c20-844f50a491b7)

Deployment in Kubernetes provides a way to manage and maintain the desired state of your application, handle scaling, and enable updates and rollbacks in a controlled manner.
### Let's create a deployment

To create deployment we need to run this `alb-ingress-controller.yaml` [file](https://github.com/aadilraza339/k8s-aws-eks-fargate-and-devops/blob/main/nginx-deployment-withrolling.yaml) for deployment.

Output:
![image](https://github.com/aadilraza339/k8s-aws-eks-fargate-and-devops/assets/47937273/ed4a7f0a-c026-4391-bfa7-2a2fdd8ac295)

Now let's check our create pods with the help of this command:

```
kubectl get all
```
Output

![image](https://github.com/aadilraza339/k8s-aws-eks-fargate-and-devops/assets/47937273/2d642d74-ec6e-435a-bdda-0d214f212493)

So now let's delete one of the pods. And then this replica set has the three desired part and three current pods.
As soon as I delete one of the pods, the replicas will see that the desired pod is three, but the current part is two.
So it is going to spring up on their part.

I am going to delete this pod with the `specific` name.
**Delete command**

```
kubectl delete pod <pod-name> | kubectl delete pod testdeploy-5f57f78888-6xxrn
```
Output
![image](https://github.com/aadilraza339/k8s-aws-eks-fargate-and-devops/assets/47937273/f122f40d-f661-4af6-9d2c-bca3667e2580)


As you can see I deleted the name of pod `testdeploy-5f57f78888-6xxrn` and in the output you can see this pod was replaced with a new one.

### Let's delete the replica now

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
Now I am going to update the container image version from `image: nginx:1.16` to `image: nginx:1.17`.

For a better understanding see this image below.
![image](https://github.com/aadilraza339/k8s-aws-eks-fargate-and-devops/assets/47937273/c5c0a6fe-1106-47dc-8dfe-e380fd46be8e)

### Let rollupdate
Make the change `image: nginx:1.16` to `image: nginx:1.17` in file `nginx-deployment-withrolling.yaml`

Let run now
Command:

```
kubectl apply -f nginx-deployment-withrolling.yaml
```

![image](https://github.com/aadilraza339/k8s-aws-eks-fargate-and-devops/assets/47937273/370b6a98-f7bf-4ff4-aa5a-ed52a77c9eb2)

So you have this new replica set, which has all three pods and the old one has no pods.

Remember before we tried to delete a replica set and it came back instantly. But now if I delete the old replica set with no pods running underneath it, should get deleted.

So delete the `old replica`
```
kubectl delete rs testdeploy-5f57f78888
```
The old replica got deleted
Output

![image](https://github.com/aadilraza339/k8s-aws-eks-fargate-and-devops/assets/47937273/c5e351e4-1072-45c2-80ff-7b1aa2125db7)

Now let's check whether our Nginx image gets updated or not.
```
kubectl describe pods
```

### Now let's talk about services
Let's start by looking at a life of a simple part.
![image](https://github.com/aadilraza339/k8s-aws-eks-fargate-and-devops/assets/47937273/a0f7d82a-70be-4847-a9a3-bdeb51d38332)
</br>
At this point, we know how to deploy pods with specific content and images. </br>
**So let's say we have two nodes.**

Both nodes have separate IP addresses and each of these pods has its own unique IP address.
Pods within the same cluster can communicate with each other.
One way for the webserver to communicate with these pods is by directly using their IP addresses.
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

This is an abstract way to expose an application running on a set of pods as a network service.
Service in Kubernetes provides a consistent network endpoint for a group of pods, allowing for load balancing, service discovery, and seamless scaling and updates.

### How does it discover the pods and know that it has to manage it?
![image](https://github.com/aadilraza339/k8s-aws-eks-fargate-and-devops/assets/47937273/6b65a65c-ae62-4de4-a468-dd2291ed9c6f)
So when you define a service. You have to give a level selector, so you basically have to say, hey, manage any pods where the same level
The app is called Front End.

Since this level matches, this service knows that, OK, I have to distribute the traffic to these.
Also, this service keeps track of any new pod that comes up with this level and it automatically adds.

### Now we create load balancer
![image](https://github.com/aadilraza339/k8s-aws-eks-fargate-and-devops/assets/47937273/ba2a3c7a-ccf6-447a-b791-a06318de32cc)

Before applying the load balancer, we need to ensure that the EKS cluster is up and running. To create a new EKS cluster, follow these steps

User this command to create eks cluster.
```
eksctl create cluster --name aadil-test --node-type t2.micro --region ap-south-1
```
It will take some time to create...

![image](https://github.com/aadilraza339/k8s-aws-eks-fargate-and-devops/assets/47937273/40d16711-7d11-4ac3-af4f-77dc5c149637)
Also, you can see it here on the AWS console.
![image](https://github.com/aadilraza339/k8s-aws-eks-fargate-and-devops/assets/47937273/27930627-f93c-4cab-bf10-22a591b14a25)
Also as you can see here I have two instances here.
![image](https://github.com/aadilraza339/k8s-aws-eks-fargate-and-devops/assets/47937273/dd9a2884-ddbf-477d-b2a0-183961d440c1)

Now apply load-balancer for this eks cluster.
Use this command:
Use this file `loadbalancer-service.yaml`
```
kubectl apply -f loadbalancer-service.yaml 
```

Output:
![image](https://github.com/aadilraza339/k8s-aws-eks-fargate-and-devops/assets/47937273/3cd770ee-047b-4b75-9efb-05bbf2b9c166)
Also, we can see it on the AWS console.
![image](https://github.com/aadilraza339/k8s-aws-eks-fargate-and-devops/assets/47937273/08be79c2-1a50-4cb5-a437-0bcf75adaed9)
 
Now with the help of DNS, we can open our Nginx image through load-balancer.
![image](https://github.com/aadilraza339/k8s-aws-eks-fargate-and-devops/assets/47937273/f0d75c28-9a3b-4290-a020-f86a8da02f5c)

In my case this is my DNS, we can open it to our Nginx in the browser
`a3d22dfe7c01a444a809c7a02c8d2a86-1880745187.ap-south-1.elb.amazonaws.com`

Output:
![image](https://github.com/aadilraza339/k8s-aws-eks-fargate-and-devops/assets/47937273/018122cd-0221-4aca-9b03-0fd482ddc598)


Now will delete my eks cluster and load-balancer.
By command you can delete the cluster.
```
eksctl delete cluster aadil-test

```
And to delete load-balance we can use aws console.
![image](https://github.com/aadilraza339/k8s-aws-eks-fargate-and-devops/assets/47937273/3b565180-8125-4c9c-a021-fcd31e33b770)



# EKS basic

### What is EKS

**AWS Manages Kubernetes control Plan**

- AWS maintain High Availability - Multiple EC2s in Multiple A-Zs
- AWS detects and replaces Unhealthy Control plan instances
- AWS Scales Control Plane
- AWS maintain ETCD
- Provides Automated version upgrade and patching
- supports Native and Upstream Kubernetes
- Integrated with AWS Ecosystem

We have three type of EKS data plane
![image](https://github.com/aadilraza339/k8s-aws-eks-fargate-and-devops/assets/47937273/20b8b343-235c-46d5-831f-97b5b2a26f30)

![image](https://github.com/aadilraza339/k8s-aws-eks-fargate-and-devops/assets/47937273/68d272a1-32c0-4ebd-b34a-a229967ff8cc)

### EKS in AWS Ecosystem

![image](https://github.com/aadilraza339/k8s-aws-eks-fargate-and-devops/assets/47937273/0c588e61-60d8-4b2b-a56e-8222bee65c60)

### Ways to spin Up Cluster

![image](https://github.com/aadilraza339/k8s-aws-eks-fargate-and-devops/assets/47937273/1a4ccbdd-6a18-4d7e-9b7b-811df4938fcb)

There is also one more to create EKS using **DevOps tools**, it is recommended for production environment.

### What is eksctl?
- CLI tool for creating clusters on EKS
- Easier than console, for real!
- Abstracts lots of stuff -VPC, Subnet, Sec, Group ect.

### eksctl commands </br>
![image](https://github.com/aadilraza339/k8s-aws-eks-fargate-and-devops/assets/47937273/d3e85050-cb00-4f61-a9a0-a7c26476c293)



### What is kubectl?

![image](https://github.com/aadilraza339/k8s-aws-eks-fargate-and-devops/assets/47937273/76070f2d-d357-438d-81d0-baca2a0aa8df)

### kubectl command syntax

![image](https://github.com/aadilraza339/k8s-aws-eks-fargate-and-devops/assets/47937273/6a24facb-6b9f-44e7-959e-75188d3ba013)

### Some are example here!
![image](https://github.com/aadilraza339/k8s-aws-eks-fargate-and-devops/assets/47937273/2963e899-3822-4597-a35b-ecac3bfa1074)


# Demo Time
- Spin up EkS cluster using eksctl
- Use kubectl
  - Deploy nginx using manifest file.
  - Get resources info


I am skipping the installation part; you can install it on your own.
- Install kubectl
- Install EksCtl
### eksctl commands

![image](https://github.com/aadilraza339/k8s-aws-eks-fargate-and-devops/assets/47937273/a76dea6f-29f3-4365-b49e-0061cfdf8610)

### Lets create out first cluster in eksctl CLI

The command for creating eks cluster

```
eksctl create cluster --name eksctl-test-aadil --nodegroup-name ng-default --node-type t3.micro --nodes 2

```
It will take some to create eks cluster

Output: </br>
![image](https://github.com/aadilraza339/k8s-aws-eks-fargate-and-devops/assets/47937273/ecda5ff1-6ee9-417d-bc49-2df5ccbab9dd)

Now will create node group for our created cluster using yaml file
![image](https://github.com/aadilraza339/k8s-aws-eks-fargate-and-devops/assets/47937273/1f462a0d-9bd0-4f1b-967a-2169e58c36f0)
**Run command**
[file](https://github.com/aadilraza339/k8s-aws-eks-fargate-and-devops/blob/main/eksctl-create-ng.yaml)
```
eksctl create nodegroup --config-file=eksctl-create-ng.yaml
```
Now we have three nodegroup, one is default when created clust by eksctl cli and two more now.
![image](https://github.com/aadilraza339/k8s-aws-eks-fargate-and-devops/assets/47937273/1ae788c8-d30c-4d20-b682-9de368f9baa6)

Now we can delete the created cluster by using below command
```
eksctl delete cluster eksctl-test-aadil
```


### EC2 instance Type and Pod limit

- Max number of allowed pods depends on EC2 instance type
- Bigger the instance type, more pods

Now again will create a cluster and will check the pods limit

We can create a cluster with same above command.

![image](https://github.com/aadilraza339/k8s-aws-eks-fargate-and-devops/assets/47937273/d74bde73-adf6-4479-807e-47ac8cbc9773)

Now we will create deployment using this yaml [file](https://github.com/aadilraza339/k8s-aws-eks-fargate-and-devops/blob/main/nginx-deployment.yaml)

With one replica `replicas: 1`
```
kubectl apply -f nginx-deployment.yaml 
```

Check pods now
![image](https://github.com/aadilraza339/k8s-aws-eks-fargate-and-devops/assets/47937273/a30e68c1-e661-4b72-9ce0-032df6d04a8b)


Now will update the replica from `replicas: 1` to `replicas: 10`
![image](https://github.com/aadilraza339/k8s-aws-eks-fargate-and-devops/assets/47937273/72d556e4-d0fc-4913-982c-87647c961829)

We can see here because of limit in the instance type in t3.micro we can't have more pods.































