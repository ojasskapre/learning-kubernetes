### Manually deploying the application

[Sample Voting Application](https://github.com/dockersamples/example-voting-app)

- docker run -d --name redis-db redis:alpine
- docker run -d --name postgres-db -v /Users/ojaskapre/projects/test:/var/lib/postgresql/data -e POSTGRES_HOST_AUTH_METHOD=trust -e POSTGRES_USER=postgres -e POSTGRES_PASSWORD=postgres postgres:15-alpine

- docker build -t webapp ./vote
- docker run -d --name webapp -p 5000:80 --link redis-db:redis webapp

- docker build -t worker ./worker
- docker run -d --name worker --link redis-db:redis --link postgres-db:db worker

- docker build -t result ./result
- docker run -d --name result -p 5001:80 --link postgres-db:db result

### Deploying using docker-compose

[docker-compose.yml](./docker-compose.yml)

```bash
docker compose up --build

docker compose down
```

### Deploying using Kubernetes
