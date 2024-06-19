# microservices-deployment

Sample microservices deployment on k8s with reverse proxy, monitoring, logging features...

## Stack

In this project you'll use following tools and applications:

- K8s/Helm
- Prometheus + Grafana
- Nginx

Now let's start the journey!

## 1. Deploy the namespace

```bash
# monitor
kubectl create ns monitoring
# app
kubectl create ns demoapp

# Check namespaces
kubectl get ns
```

## 2. Deploy the application

```bash
kubectl apply -f https://raw.githubusercontent.com/GoogleCloudPlatform/microservices-demo/main/release/kubernetes-manifests.yaml -n demoapp
# To debug, run: kubectl get services -n  demoapp
```

## 3. Deploy Nginx proxy

Deploy the Nginx proxy prior to `fontend` service

```bash
kubectl apply -f manifest/nginx/nginx.yaml -n demoapp
# To debug, run: kubectl get services -n  demoapp
```

## 4. Expose Nginx service

Open another terminal and run:

```bash
# Note: You can also replace 8090 to the working PORT in your PC
kubectl port-forward service/nginx-ingress -n demoapp 8090:80
```

Now visit http://localhost:8090/ to access your application locally:

![nginx-ok](assets/nginx-ok.png)

## 5. Prometheus + Grafana

### Deploy Prometheus/Grafana stack

<!-- ![prometheus-architecture](assets/prometheus-architecture.png) -->

- In section #4 we've successfully deploy our microservices application on k8s, deploy the Nginx proxy infont of the fontend app.
- Now let's monitor our application with Prometheus + Grafana
- We will use Helm...

```bash
# Prepare
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
# Deploy
helm install kube-prometheus-stack prometheus-community/kube-prometheus-stack --namespace monitoring
```

- Verify installation

```bash
# Pod checking
kubectl --namespace monitoring get pods -l "release=kube-prometheus-stack"
# All resources checking
kubectl --namespace monitoring get all
```

- Ref
  - https://artifacthub.io

### Access the dashboard

- Expose Grafana

```bash
kubectl port-forward svc/kube-prometheus-stack-grafana -n monitoring 4000:80
```

- Expose Prometheus

```bash
kubectl port-forward svc/kube-prometheus-stack-prometheus -n monitoring 4001:9090
```

Now we can login to http://localhost:4000 (The default username/password for Grafana is `admin/prom-operator`)
