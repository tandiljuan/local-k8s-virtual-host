Local k8s development environment with name-based virtual hosting
=================================================================


#1 Required tools
-----------------

- [docker](https://www.docker.com/)
- [k3d](https://github.com/k3d-io/k3d)
- [hostctl](https://github.com/guumaster/hostctl)


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
