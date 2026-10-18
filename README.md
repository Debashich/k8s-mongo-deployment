# k8s-mongo-deployment

A minimal **Kubernetes** deployment that runs a **MongoDB** instance together with **Mongo‑Express** (a web UI for MongoDB). The manifests include:

- `mongo.yml` – MongoDB Deployment & Service
- `mongo-secret.yml` – Secret containing the root username/password (Base64‑encoded)
- `mongo-configmap.yml` – ConfigMap with the MongoDB service hostname
- `mongo-express.yml` – Mongo‑Express Deployment (with Basic‑Auth) and Service

## Prerequisites

- **minikube** (or any Kubernetes cluster) with the Docker driver
- `kubectl` configured to point at the cluster
- Docker installed (for the `mongo-express` image pull)

## Quick start

```bash
# Clone the repo (if not already)
git clone https://github.com/Debashich/k8s-mongo-deployment
cd k8s-mongo-deployment

# Apply all manifests
kubectl apply -f mongo.yml
kubectl apply -f mongo-secret.yml
kubectl apply -f mongo-configmap.yml
kubectl apply -f mongo-express.yml
```

### Verify pods are running

```bash
kubectl get pods -l app=mongodb
kubectl get pods -l app=mongo-express
```

You should see both pods in the **Running** state.

### Expose Mongo‑Express UI

```bash
# Open a tunnel to the LoadBalancer service (minikube creates a local URL)
minikube service mongo-express-service
```

The command prints a URL such as `http://127.0.0.1:36043`. Open it in a browser.

## Clean up

```bash
kubectl delete -f mongo-express.yml
kubectl delete -f mongo.yml
kubectl delete -f mongo-secret.yml
kubectl delete -f mongo-configmap.yml
```

## Troubleshooting

- **Logs** – View logs for any pod:
  ```bash
  kubectl logs -l app=mongo-express -c mongo-express
  ```
- **Service not reachable** – Ensure `minikube service` is still running; the tunnel must stay open while you use the UI.
- **Authentication failures** – Verify that the secret keys match the env vars `ME_CONFIG_BASICAUTH_USERNAME` and `ME_CONFIG_BASICAUTH_PASSWORD` in `mongo-express.yml`.