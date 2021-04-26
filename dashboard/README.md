# Deploy dashboard on k8s
Dashboard is a web-based Kubernetes user interface. You can use Dashboard to deploy containerized applications to a Kubernetes cluster, troubleshoot your containerized application, and manage the cluster resources.
Official project repository can be accessed here: https://github.com/kubernetes/dashboard

## Deploying dashboard
To deploy kubernetes dashboard first clone this repository and then execute below command.

~~~~
kubectl apply -f dashboard/manifests/dashboard.yaml
~~~~

Assuming that every thing is working correctly now you should create an admin user to be able to access dashboard web UI. Execute below command to create admin user.

~~~~
kubectl apply -f dashboard/manifests/admin-user.yaml
~~~~

Now run below command to generate a token for admin user.

~~~~
kubectl -n kubernetes-dashboard get secret $(kubectl -n kubernetes-dashboard get sa/admin-user -o jsonpath="{.secrets[0].name}") -o go-template="{{.data.token | base64decode}}"
~~~~

## Accessing kubernetes dashboard
Since kubernetes dashboard service is `NodePort` based on below configuraton in `dashboard.yaml` file you can access web UI by entering `workerNodeIP:32080` on browser and select `token` for authentication then paste admin token which has been created in previous step.

~~~~
kind: Service
apiVersion: v1
metadata:
  labels:
    k8s-app: kubernetes-dashboard
  name: kubernetes-dashboard
  namespace: kubernetes-dashboard
spec:
  type: NodePort
  ports:
    - port: 443
      targetPort: 8443
      nodePort: 32080
  selector:
    k8s-app: kubernetes-dashboard
~~~~
