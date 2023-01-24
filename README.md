Local k8s development environment with name-based virtual hosting
=================================================================


#1 Required tools
-----------------

- [docker](https://www.docker.com/)


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
