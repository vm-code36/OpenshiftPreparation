Here’s a **zero → hero Docker command practice list** you can run in order. Copy/paste and try them one by one.

---

## 1) Setup sanity

```bash
docker version
docker info
docker help
```

---

## 2) Images basics

```bash
docker images
docker search nginx
docker pull nginx:alpine
docker image inspect nginx:alpine
docker image history nginx:alpine
docker rmi nginx:alpine   # remove (if not used by containers)
```

---

## 3) Run containers (core skill)

```bash
docker run hello-world
docker run --name webapp nginx:alpine
docker ps
docker ps -a
docker logs webapp
docker stop webapp
docker start webapp
docker rm webapp
```

Run in background + port mapping:

```bash
docker run -d --name webapp -p 8080:80 nginx:alpine
```

---

## 4) Exec, shell, and debugging inside containers

```bash
docker exec -it webapp sh
# inside container:
ls
cat /etc/os-release
exit
```

---

## 5) Copy files in/out

Copy from container to host:

```bash
docker cp webapp:/etc/nginx/nginx.conf ./nginx.conf
```

Copy from host to container:

```bash
docker cp ./nginx.conf webapp:/etc/nginx/nginx.conf
docker restart webapp
```

---

## 6) Networking (must-know)

See networks:

```bash
docker network ls
docker network inspect bridge
```

Create a custom network and connect containers:

```bash
docker network create mynet
docker run -d --name web1 --network mynet nginx:alpine
docker run -it --rm --network mynet alpine sh
# inside alpine:
apk add --no-cache curl
curl http://web1
exit
```

---

## 7) Volumes (persist data)

Create a volume:

```bash
docker volume ls
docker volume create mydata
docker volume inspect mydata
```

Use volume with nginx html:

```bash
docker run -d --name webvol -p 8081:80 -v mydata:/usr/share/nginx/html nginx:alpine
```

Write content into the volume using a helper container:

```bash
docker run --rm -v mydata:/data alpine sh -c 'echo "Hello from volume" > /data/index.html'
```

Refresh:

```bash
curl http://localhost:8081
```

Bind mount (map local folder):

```bash
mkdir -p site && echo "Hello bind mount" > site/index.html
docker run -d --name webbind -p 8082:80 -v "$PWD/site":/usr/share/nginx/html:ro nginx:alpine
curl http://localhost:8082
```

---

## 8) Logs + resource monitoring

```bash
docker logs -f webapp
docker stats
docker top webapp
docker inspect webapp
```

---

## 9) Clean up (important habit)

```bash
docker stop webapp web1 webvol webbind 2>/dev/null
docker rm webapp web1 webvol webbind 2>/dev/null
docker network rm mynet 2>/dev/null
docker volume rm mydata 2>/dev/null
```

Prune unused stuff (careful):

```bash
docker system df
docker system prune
docker image prune
docker volume prune
```

---

## 10) Build your own image (Dockerfile basics)

Create a file named `Dockerfile`:

```dockerfile
FROM nginx:alpine
COPY site/ /usr/share/nginx/html
```

Build and run:

```bash
docker build -t mynginx:1 .
docker run -d --name mynginx -p 8083:80 mynginx:1
curl http://localhost:8083
```

---

## 11) Docker Compose (real-world)

Create `compose.yaml`:

```yaml
services:
  web:
    image: nginx:alpine
    ports:
      - "8084:80"
```

Run:

```bash
docker compose up -d
docker compose ps
docker compose logs -f
docker compose down
```

---

## 12) Tag + push (optional)

```bash
docker tag mynginx:1 yourdockerhubuser/mynginx:1
docker login
docker push yourdockerhubuser/mynginx:1
```

---
