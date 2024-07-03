# Bologna Digital Twin Data Platform and Execution Infrastructure

This repository provides deployment guidelines and some example use cases for the GDB data platform and its corresponding execution infrastructure.

The deployment and usage instructions for each use case can be found in the *docs* folder.

The platform deployment is managed via Docker Compose. Each documented use case requires a subset of components and has a corresponding YAML file in the *docker-compose* folder. The same folder also contains Docker Compose files for single components.

## Installation on minikube

### Prerequisites 
- [Helm](https://helm.sh/docs/intro/install/)
- [Kubectl](https://kubernetes.io/docs/tasks/tools/#kubectl)
- [Minikube](https://minikube.sigs.k8s.io/docs/start/)

### Installation

1. Start minikube (change 192.168.49.0 if your network setup is different):
```sh
    minikube start --insecure-registry "192.168.49.0/24" --memory 8192 --cpus 4
```
2. Get minikube external IP:
```sh
    minikube ip
```
3. Change the IP in  'global.registry.url' and 'global.externalHostAddress' properties in values file (*helm/digitalhub/values.yaml*) with the one obtained in the previous step.
4. Add Digitalhub repository:
```sh
helm repo add digitalhub https://github.com/Gemello-Digitale-Bologna/dataplatform/
```
5. Install DataPlatform with Helm:
```sh
    helm upgrade digitalhub digitalhub/digitalhub -n digitalhub --install --create-namespace --timeout 15m0s
```
6. Wait until all pods are in Running state
```sh
    kubectl --namespace digitalhub get pods
```
7. Retrieve database and S3 secrets
```sh
    kubectl --namespace digitalhub get secret minio -o yaml
    kubectl --namespace digitalhub get secret digitalhub-owner-user.database-postgres-cluster.credentials.postgresql.acid.zalan.do -o yaml
```
8. Decode secret values
```sh
    echo -n "<BASE64_VALUES_FROM_SECRET>" | base64 -d 
```
9. Create secret with previously decoded values
```
    kubectl -n digitalhub create secret generic digitalhub-common-creds --from-literal=POSTGRES_USER=<DECODED_VALUE> --from-literal=POSTGRES_PASSWORD=<DECODED_VALUE> --from-literal=AWS_ACCESS_KEY_ID=<DECODED_VALUE> --from-literal=AWS_SECRET_ACCESS_KEY=<DECODED_VALUE>
```
