# Validation Instructions

Follow these steps to deploy and validate the ToDo application and its volume mounts in the Kubernetes cluster.

## 1. Deploy Infrastructure

Run the bootstrap script to create all resources:

```bash
chmod +x bootstrap.sh
./bootstrap.sh
```

---

## 2. Validate App is Running

Check the status of the pods in the `todoapp` namespace:

```bash
kubectl get pods -n todoapp
```

Verify that the Deployment is active and available:

```bash
kubectl get deployment todoapp -n todoapp
```

You can also check the health endpoint of the application container:

```bash
kubectl exec -n todoapp deployment/todoapp -- curl -s http://localhost:8080/api/health
```

Expected output:
```json
{"status": "ok"}
```

---

## 3. Validate ConfigMap Data Mount

Verify that the `app-config` ConfigMap is mounted as a read-only file under `/app/configs`:

```bash
kubectl exec -n todoapp deployment/todoapp -- ls -la /app/configs
```

Check the content of the `PYTHONUNBUFFERED` file:

```bash
kubectl exec -n todoapp deployment/todoapp -- cat /app/configs/PYTHONUNBUFFERED
```

Expected output:
```text
1
```

---

## 4. Validate Secret Data Mount

Verify that the `app-secret` Secret is mounted as a read-only file under `/app/secrets`:

```bash
kubectl exec -n todoapp deployment/todoapp -- ls -la /app/secrets
```

Check the content of the `SECRET_KEY` file:

```bash
kubectl exec -n todoapp deployment/todoapp -- cat /app/secrets/SECRET_KEY
```

---

## 5. Validate Persistent Volume Mount

Verify that the PersistentVolumeClaim is bound to the PersistentVolume:

```bash
kubectl get pv pv-data
kubectl get pvc pvc-data -n todoapp
```

Verify that the storage volume is mounted inside the container at `/app/data`:

```bash
kubectl exec -n todoapp deployment/todoapp -- ls -la /app/data
```