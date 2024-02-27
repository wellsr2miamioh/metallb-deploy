# prometheus
Configuration for Prometheus on Kubernetes Clusters

1) Start by creating a prometheus namespace: 
  kubectl create namespace prometheus

2) Add the prometheus-config.yaml file to the cluster.

3) Apply the prometheus-config.yaml
   kubectl apply -f prometheus-config.yaml -n prometheus

4) Add the prometheus-deployment.yaml file to the cluster.

5) Apply the prometheus-deployment.yaml
     kubectl apply -f prometheus-deployment.yaml -n prometheus

6) Add the prometheus-servec.yaml file to the cluster.

7) Apply the prometheus-service.yaml file to the cluster.
     kubectl apply -f prometheus-service.yaml -n prometheus

8) Determine the external IP address of the load balancer by running:
     kubectl get service prometheus-service -n prometheus
