# Reverse Proxy for OCI ATP

## Purpose

When Oracle first announced all of the [free-tier resources](https://www.oracle.com/cloud/free/) they offer I immediately jumped on the offer. Thankfully, [Dimitri Gielis](https://twitter.com/dgielis?lang=en) had already started on his AMAZING [Best and Cheapest Oracle APEX hosting: Free Oracle Cloud](https://dgielis.blogspot.com/2019/09/best-and-cheapest-oracle-apex-hosting.html) blog post series. While I don't use things like APEX Office Print - his guides on using a compute instance as a nginx reverse proxy were super helpful.

Now that I've configured an [OKE](https://www.oracle.com/cloud-native/container-engine-kubernetes/) cluster (on ARM for free!) and use an [OCI Load Balancer](https://docs.oracle.com/en-us/iaas/Content/Balance/Concepts/balanceoverview.htm) to route my OCI traffic - I wanted to move the simple Nginx reverse proxy into Kubernetes. This repo is quite simple and creates a nginx service with a custom config file. I also use [Traefik](https://traefik.io/traefik/), so it creates a Traefik IngressRoute for my subdomain.

## How to use
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