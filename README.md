# openwhisk-blog

[Apache OpenWhisk](https://openwhisk.apache.org/) is an open source, distributed Serverless platform that executes functions (fx) in response to events at any scale. The OpenWhisk platform supports a programming model in which developers write functional logic (called Actions), in any supported programming language, that can be dynamically scheduled and run in response to associated events (via Triggers) from external sources (Feeds) or from HTTP requests.

You can install OpenWhisk on your [Kubernetes](https://kubernetes.io/) cluster, there are only some limitations: version 1.16+, ability to create Ingresses and a default StorageClass that supports Dynamic Volume Provision.

This means you can choice beetwen every type of Kubernetes installation you prefer: provision a cluster from a cloud provider (GKE, EKS etc..), or an "hard-way installation" on bare metal are both fine.

Keep in mind that you need at least 1 worker node with 4GB of memory and 2 virtual CPUs to deploy the default configuration and that you can scale-up the replicas of the various component when the load will increase.

The [helm](https://helm.sh/) chart provided by https://github.com/apache/openwhisk-deploy-kube is the best option for an easy install. 
Helm helps you to find, share, and use software built for Kubernetes, it simplifies the deployment and management of applications on Kubernetes clusters. The OpenWhisk Helm chart requires the Helm 3

This little guide will cover the steps required to have OpenWhisk up and running on [kind](https://kind.sigs.k8s.io/). If you have linux, it's very easy to deploy a cluster on your laptop for testing pourpose.

- Install kubectl:
```
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
```

- Install kind:
```
curl -Lo ./kind "https://kind.sigs.k8s.io/dl/v0.10.0/kind-$(uname)-amd64"
chmod +x ./kind
mv ./kind /usr/local/bin/kind
```
- Create cluster:
```
kind create cluster # Default cluster context name is `kind`.
```
- Install helm:
If you are using a Debian-based distribution you can install helm with:

```
curl https://baltocdn.com/helm/signing.asc | sudo apt-key add -
sudo apt-get install apt-transport-https --yes
echo "deb https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
sudo apt-get update
sudo apt-get install helm
```

- Initial Setup:
```
kubectl label nodes --all openwhisk-role=invoker
```

- Deploy OpenWhisk from Git
```
git clone https://github.com/apache/openwhisk-deploy-kube.git
helm install owdev ./helm/openwhisk -n openwhisk --create-namespace -f mycluster.yaml
```

- Install [openwhisk-cli](https://github.com/apache/openwhisk-cli)
```
curl https://openwhisk.ng.bluemix.net/cli/go/download/linux/amd64/wsk -o wsk && chmod +x ./wsk && mv ./wsk /usr/local/bin
```
- Configure openwhisk-cli
```
wsk property set --apihost <whisk.ingress.apiHostName>:<whisk.ingress.apiHostPort>
wsk property set --auth 23bc46b1-71f6-4ed5-8c54-816aa4f8c502:123zO3xZCLrMN6v2BKK1dXYFpXlPkccOFqm12CdAsMgRU4VrNZ9lyGVCGuMDGIwP
```
- Verify and interact with your OpenWhisk Deployment (-i disable SSL checking)
```
wsk -i action list
```
