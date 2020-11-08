# Readme

A guide on creating a Kubernetes cluster on AWS EKS using eksctl. 

# AWS IAM user

Make an IAM user with the following permissions:

https://eksctl.io/usage/minimum-iam-policies/


## Install eksctl

Info taken from here https://docs.aws.amazon.com/eks/latest/userguide/getting-started-eksctl.html

> curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp

> sudo mv /tmp/eksctl /usr/local/bin

> eksctl version

## Install AWS IAM authenticator

Info taken from https://docs.aws.amazon.com/eks/latest/userguide/install-aws-iam-authenticator.html

 > curl -o aws-iam-authenticator https://amazon-eks.s3.us-west-2.amazonaws.com/1.18.8/2020-09-18/bin/linux/amd64/aws-iam-authenticator

> chmod +x ./aws-iam-authenticator

> mkdir -p $HOME/bin && cp ./aws-iam-authenticator $HOME/bin/aws-iam-authenticator && export PATH=$PATH:$HOME/bin

> echo 'export PATH=$PATH:$HOME/bin' >> ~/.bashrc

> aws-iam-authenticator help

## Create a cluster.yaml file

Info taken from here : https://eksctl.io/

```
apiVersion: eksctl.io/v1alpha5
kind: ClusterConfig

metadata:
  name: gordon
  region: eu-west-1

nodeGroups:
  - name: spot-nodes
    labels: { role: spots }
    minSize: 2
    maxSize: 3
    instancesDistribution:
        maxPrice: 0.030
        instanceTypes: ["t3.large"] # At least one instance type should be specified
        onDemandBaseCapacity: 0
        onDemandPercentageAboveBaseCapacity: 50
        spotInstancePools: 1
    volumeSize: 50
    ssh:
      allow: true
      publicKeyPath: ~/.ssh/id_rsa.pub
    tags:
      nodegroup-role: worker
```

### Create the cluster

> eksctl create cluster -f cluster.yaml

### Deploy a Guestbook sample app

> kubectl apply -f https://raw.githubusercontent.com/kubernetes/examples/master/guestbook-go/redis-master-controller.json

> kubectl apply -f https://raw.githubusercontent.com/kubernetes/examples/master/guestbook-go/redis-master-service.json

> kubectl apply -f https://raw.githubusercontent.com/kubernetes/examples/master/guestbook-go/redis-slave-controller.json

> kubectl apply -f https://raw.githubusercontent.com/kubernetes/examples/master/guestbook-go/redis-slave-service.json

> kubectl apply -f https://raw.githubusercontent.com/kubernetes/examples/master/guestbook-go/guestbook-controller.json

> kubectl apply -f https://raw.githubusercontent.com/kubernetes/examples/master/guestbook-go/guestbook-service.json 

Use kubectl to see a list of your services:

> kubectl get svc

Open the LB address on port 3000 ( may take a minute )

### Delete cluster

> eksctl delete cluster -f cluster.yaml

