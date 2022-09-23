# Local_cluster_repo
This repo serves as a starting point to explore and learn GitOps tooling in a local Kubernetes cluster.

Steps to install local cluster:

#Clone repo (insights-in-a-box/cluster.yaml)

# 1.Spin up a local registry

docker run \
  -d \
  --restart=always \
  -p "127.0.0.1:5000:5000" \
  --name "kind-registry" \
  registry:2

# 2.Create our cluster 
kind create cluster --config=git/insights-in-a-box/cluster.yaml 

# 3.Cut you a token for great GitOps!
set -x GITLAB_TOKEN <GITLAB_TOKEN>

# 4.Install flux version 0.31.5

brew install fluxcd/tap/flux

# 5.Bootstrap (this'll take a few minutes)
flux bootstrap gitlab \
  --context kind-kind \
  --personal \
  --owner=<YOUR_USERNAME_FOR_FORK> \
  --repository=insights-in-a-box \
  --branch=master \
  --token-auth \
  --verbose \
  --path=./operations

# 6.Bridge docker networks
docker network connect kind kind-registry

# 7.Copy an image to our local repo

docker pull centos:8

docker tag centos:8 localhost:5000/centos:8

docker push localhost:5000/centos:8



# 8.Validate that we can pull images from the local repo

   docker pull localhost:5000/centos:8
