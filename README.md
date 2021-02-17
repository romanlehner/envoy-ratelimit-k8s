# Ratelimit Test on Kubernetes
This repo represents the kubernetes version of the ratelimit docker-compose configuration from the creators of this [envoy ratelimit application](https://github.com/envoyproxy/ratelimit).

## Create cluster (K3D)
    k3d cluster create ratelimit-demo --api-port 6550 -p 80:80@loadbalancer

## Deploy Istio

    curl -L https://istio.io/downloadIstio | ISTIO_VERSION=1.9.0 TARGET_ARCH=x86_64 sh -
    cd istio-1.9.0
    export PATH=$PWD/bin:$PATH
    istioctl install --set profile=default
    kubectl label namespace default istio-injection=enabled

## Install Prometheus

    kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.9/samples/addons/prometheus.yaml

### Install Kiali

    kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.9/samples/addons/kiali.yaml

    istioctl dashboard kiali

## Deploy Test Setup 

    kubectl apply -f .k8s/envoy.yaml,.k8s/ratelimit.yaml,.k8s/redis.yaml,.k8s/statsd.yaml

## Run Ratelimit test
Port forward the envoy-proxy pod to port 8888 and run a few curl commands as below. Subsequent requests should get blocked for a while.

    for i in {1..5}; do curl localhost:8888/header -H "foo: foo"; done
