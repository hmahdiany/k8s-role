# Deploy metric-server on k8s
Metrics Server is a scalable, efficient source of container resource metrics for Kubernetes built-in autoscaling pipelines.
Metrics Server collects resource metrics from Kubelets and exposes them in Kubernetes apiserver through Metrics API for use by `Horizontal Pod Autoscaler` and `Vertical Pod Autoscaler`. Metrics API can also be accessed by kubectl top, making it easier to debug autoscaling pipelines.

## Deploying metric-server manifests
Deploying metric-server is straight forward. Just clone this repository and execute below command.

~~~~
kubectl apply -f metric-server/manifests/components.yaml
~~~~

Make sure metric-server pod is running in kube-system namespace.

## Accessing metric-server
Consider the fact that every thing is working fine you can access metric-server metrics with below commands:

~~~~
kubectl top nodes
kubectl top pods
~~~~
