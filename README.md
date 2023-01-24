Local k8s development environment with name-based virtual hosting
=================================================================


#1 Required tools
-----------------

- [docker](https://www.docker.com/)
- [k3d](https://github.com/k3d-io/k3d)
- [hostctl](https://github.com/guumaster/hostctl)
- [kubectl](https://kubernetes.io/docs/tasks/tools/#kubectl)
- [tilt](https://github.com/tilt-dev/tilt)


#2 Build and test WEB container (with docker)
---------------------------------------------

Build **potato web** image.

```bash
docker build --rm --tag 'potato/web' potato-web
```

Run **potato web** container.

```bash
docker run --detach --rm --name potato-web --publish 9090:8080 potato/web
```

Test **potato web** container.

```bash
curl localhost:9090 --header 'Host: blue.potato.com'
curl localhost:9090 --header 'Host: green.potato.com'
```

Stop **potato web** container.

```bash
docker stop potato-web
```


#3 Use k3d to setup a local k8s cluster
---------------------------------------

Create a local registry called `k3d-registry.potato.com`. The `k3d-` prefix will be added automatically by **k3d**.

```bash
k3d registry create registry.potato.com --port 12345
```

Add registry domain name into **hosts** file

```bash
sudo hostctl add domains potato k3d-registry.potato.com
hostctl list
```

Create a cluster called `potato-cluster` that uses the registry.

```bash
k3d cluster create potato-cluster --registry-use k3d-registry.potato.com:12345
```

Tag and push **potato web** image to the registry.

```bash
docker tag potato/web:latest k3d-registry.potato.com:12345/potato/web:0.1
docker push k3d-registry.potato.com:12345/potato/web:0.1
```


#4 Run **potato web** image in the k8s cluster
----------------------------------------------

Apply k8s configuration to run **potato web**.

```bash
kubectl apply -f potato-web.yaml
```

Request (test) *blue* and *green* pages from **potato web** within k8s cluster.

```bash
POTATO_INGRESS_IP=$(kubectl get ingress/potato-web-ingress -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
echo ">> Ingress IP: ${POTATO_INGRESS_IP}"
curl $POTATO_INGRESS_IP --header 'Host: blue.potato.com'
curl $POTATO_INGRESS_IP --header 'Host: green.potato.com'
```

Delete all artifacts created from the configuration file

```bash
kubectl delete -f potato-web.yaml
```


#5 Use tilt to run **potato web** image in k8s (k3d) cluster
------------------------------------------------------------

Start tilt.

```bash
tilt up
```

Add **potato web** domain names into **hosts** file

```bash
POTATO_INGRESS_IP=$(kubectl get ingress/potato-web-ingress -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
echo ">> Ingress IP: ${POTATO_INGRESS_IP}"
sudo hostctl add domains --ip $POTATO_INGRESS_IP potato blue.potato.com green.potato.com
hostctl list
```

Open tilt UI and click the links or open a web browser and test the **blue** and **green** domain names.

Remove **potato web** domain names from **hosts** file

```bash
sudo hostctl remove domains potato blue.potato.com green.potato.com
```

Stop tilt.

```bash
tilt down
```
