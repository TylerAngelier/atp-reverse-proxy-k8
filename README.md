# Reverse Proxy for OCI ATP

You need to create a config map that the deployment uses.

```
kubectl create configmap atp-reverse-proxy-nginx-conf --from-file=./nginx.conf
```

Next you will deploy the application. We will use the official [nginx](https://hub.docker.com/_/nginx) image published on docker hub - specifically the `stable-alpine` tag.

```
kubectl apply -f deployment/app.yaml
```

Finally you will create a [Traefik IngressRoute](https://doc.traefik.io/traefik/routing/providers/kubernetes-crd/#kind-ingressroute) to route the traffic to your pod. Check the `match` criteria in the `deployment/ingress-route.yaml` file - I use `apex.trangelier.dev` but you should change it to your domain. You also need to make any DNS & Load Balancer changes in OCI (or your LB) to accept traffic on the specificed domain.

```
kubectl apply -f deployment/ingress-route.yaml
```