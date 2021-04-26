# Deploy cAdvisor on k8s
cAdvisor is an open source project which is used for monitoring container resourt usage. Official project repository could be accessed here: https://github.com/google/cadvisor
In order to deploy cAdvisor manifests first we should install `kustomize`.

## Installing kustomize
kustomize installation is pretty simple. Just download binary file and copy it to `/usr/local/bin/`. Official kustomize repository could be accessed here: https://github.com/kubernetes-sigs/kustomize

~~~~
curl -LO https://github.com/kubernetes-sigs/kustomize/releases/download/kustomize%2Fv4.1.2/kustomize_v4.1.2_linux_amd64.tar.gz
tar xf kustomize_v4.1.2_linux_amd64.tar.gz
cp kustomize /usr/local/bin/
~~~~

## Deploying cAdvisor manifests
To deploy cAdvisor manifests first clone this project on any of control plane servers and then use below instruction. You can edit `cadvisor/deploy/kubernetes/ingress.yaml` to define your desired URL.

~~~~
cd k8s
kustomize cadvisor/deploy/kubernetes/base
kustomize cadvisor/deploy/kubernetes/base | kubectl apply -f -
~~~~

Important note: If you want to add an extra manifest in cAdvisor deployment first add your yaml file in `cadvisor/deploy/kubernetes/` directory and then edit `kustomization.yaml` which located in the same directory and add the name of your new yaml file, now if you execute above commands your new manifests will be applied.

## Accessing cAdvisor metrics
To access cAdvisor metrics we should configure HAProxy to pass request to nginx-ingress.
~~~~
frontend k8s-cadvisor
    mode http
    bind 192.168.115.42:80
    option forwardfor
    acl ACL_k8s-cadvisor hdr(host) -i cadvisor.art.net
    use_backend k8s-http-ingress if ACL_k8s-cadvisor
    default_backend k8s-http-ingress

backend k8s-http-ingress
    mode http
    option forwardfor
    server kuber-worker-n1 192.168.115.38:80 check 
    server kuber-worker-n2 192.168.115.39:80 check
~~~~

Edit `cadvisor.art.net` URL based on your configuration in `ingress.yaml`.

## Accessing cAdvisor via Prometheus
Here is a sample configuration for gathering data via Prometheus.
~~~~
- job_name: 'k8s-cadvisor-metrics'
  scrape_interval: 15s
  static_configs:
    - targets: ['cadvisor.art.net']
~~~~

Edir `targets` based on your configuration.
