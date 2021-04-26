# Deploy ingress-nginx controller on k8s
Ingress-nginx is for accessing application running on kubernetes cluster from outside. Official website address is this: https://kubernetes.github.io/ingress-nginx/

## Deploying ingress-nginx controller
Run below command to deploy ingress-nginx controller on your cluster.

~~~~
kubectl apply -f ingress-nginx/manifests/deploy.yaml
~~~~

Based on below configuration on `deploy.yaml` this controller works in host network mode which means controller pod listens on worker node ports 80 and 443.

~~~~
# Source: ingress-nginx/templates/controller-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    helm.sh/chart: ingress-nginx-3.23.0
    app.kubernetes.io/name: ingress-nginx
    app.kubernetes.io/instance: ingress-nginx
    app.kubernetes.io/version: 0.44.0
    app.kubernetes.io/managed-by: Helm
    app.kubernetes.io/component: controller
  name: ingress-nginx-controller
  namespace: ingress-nginx
spec:
  replicas: 2
  selector:
    matchLabels:
      app.kubernetes.io/name: ingress-nginx
      app.kubernetes.io/instance: ingress-nginx
      app.kubernetes.io/component: controller
  revisionHistoryLimit: 10
  minReadySeconds: 0
  template:
    metadata:
      labels:
        app.kubernetes.io/name: ingress-nginx
        app.kubernetes.io/instance: ingress-nginx
        app.kubernetes.io/component: controller
    spec:
      dnsPolicy: ClusterFirst
      hostNetwork: true
      containers:
        - name: controller
          image: k8s.gcr.io/ingress-nginx/controller:v0.44.0
          imagePullPolicy: IfNotPresent
~~~~

Also you can choose how many replicas you want to run in your cluster.

## Accessing ingress-nginx
We should configure HAProxy to pass input traffic to workers either on port 80 or 443. Here is a sample HAProxy configuration.

~~~~
backend k8s-http-ingress
    mode http
    option forwardfor
    server kuber-worker-n1 192.168.115.38:80 check
    server kuber-worker-n2 192.168.115.39:80 check
~~~~

