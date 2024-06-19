# microservices-deployment

Sample microservices deployment on k8s with reverse proxy, monitoring, logging features...

## Stack

- K8s
- Prometheus + Grafana
- Nginx

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

- In section #4 we've successfully deploy our microservices application on k8s, deploy the Nginx proxy infont of the fontend app.
- Now let's monitor our application with Prometheus + Grafana
- We will use Helm...
