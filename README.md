# Odilia Restaurant App on Kubernetes

This project deploys the Odilia restaurant voting app with Kubernetes.

The main running application is based on the Yelb images:

- `mreferre/yelb-ui:0.7`
- `mreferre/yelb-appserver:0.5`
- `mreferre/yelb-db:0.5`
- `redis:4.0.2`

The Kubernetes deployment also includes the extra Redis replica, Redis Sentinel, and database replication services listed in the assignment deck so students can see all 12 microservices.

## Architecture

- `yelb-ui`: frontend service exposed to users.
- `yelb-appserver`: API service used by the frontend.
- `redis-server`: Redis cache used by the API.
- `odilia-redis01` and `odilia-redis02`: Redis replica examples.
- `odilia-redis-sentinel01`, `odilia-redis-sentinel02`, and `odilia-redis-sentinel03`: Redis Sentinel examples.
- `yelb-db`: main database service.
- `odilia-db-replication01`, `odilia-db-replication02`, and `odilia-db-replication03`: database replica examples.

## Deploy on Minikube

Start Minikube:

```bash
minikube start --driver=docker --memory=4096mb --cpus=2
```

Apply the manifests:

```bash
kubectl apply -k k8s/
```

Check the namespace:

```bash
kubectl get all -n odilia
kubectl get pvc -n odilia
```

Wait until the main pods are running:

```bash
kubectl get pods -n odilia -w
```

## Open the App

On a local Minikube machine:

```bash
minikube service yelb-ui -n odilia --url
```

On an EC2 instance, use port-forward:

```bash
kubectl port-forward -n odilia svc/yelb-ui 8080:80 --address 0.0.0.0
```

Then open:

```text
http://EC2_PUBLIC_IP:8080
```

Make sure the EC2 security group allows inbound TCP port `8080` from your IP.

## Troubleshooting

Check pods:

```bash
kubectl get pods -n odilia
```

Check service ports:

```bash
kubectl get svc -n odilia
```

View logs:

```bash
kubectl logs -n odilia deploy/yelb-ui
kubectl logs -n odilia deploy/yelb-appserver
kubectl logs -n odilia deploy/yelb-db
kubectl logs -n odilia deploy/redis-server
```

Delete everything:

```bash
kubectl delete namespace odilia
```

## Note for Students

The Redis Sentinel and database replica services are included to match the assignment architecture. The public Yelb app talks directly to `redis-server` and `yelb-db`, so those two services are the active cache and database used by the application.

