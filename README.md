# Running Tekton pipelines demo in k8s
## 1. Prerequisites.
- k8s cluster to install the tekton pipelines instance (tested with 3 cluster nodes with 2 vCPUs and 4 GB memory).
- Nginx ingress installed (if not is installed follow [this](https://kubernetes.github.io/ingress-nginx/deploy/) procedure to install it).
- Cert-manager installed (if not is installed follow [this](https://cert-manager.io/docs/installation/helm/) procedure to install it).
- kubectl cli installed to comunicate with k8s cluster.
- Helm cli installed to deploy required resources.

## 2. Create the ClusterIssuer to generate the TLS certificates.
- Replace and set a valid email in the `<YOUR_EMAIL>` section in the [ClusterIssuer.yaml](apps/ClusterIssuer.yaml) file using the following command.

`sed -i -e "s, <EMAIL>, <YOUR_EMAIL>,g" apps/ClusterIssuer.yaml`

- Run the following commands to install the ClusterIssuer resource.

`kubectl apply -f apps/ClusterIssuer.yaml`

## 3. Add the nginx ingress public IP to all the resources.
- Run the following command to get the nginx ingress public IP.

`kubectl get svc ingress-nginx-controller -o jsonpath='{.status.loadBalancer.ingress[0].ip}' -n ingress-nginx; echo`

- Put your nginx ingress public IP on `<NGINX_INGRESS_IP>` and run the following commands to replace the nginx ingress public IP in the required resources.

`sed -i -e "s/W.X.Y.Z/<NGINX_INGRESS_IP>/g" apps/tekton/release.yaml apps/tekton/tekton-dashboard-ingress.yaml apps/grafana/grafana.yaml DemoPipelines/build-push-deploy-run.yaml`
 
## 3. Installing tekton pipelines resources.
- Run the following commands to install the latest tekton pipelines resources.

### Tekton pipelines release v0.52.0:

`kubectl apply -f apps/tekton/release.yaml`

### Tekton triggers release v0.25.0:

`kubectl apply -f https://storage.googleapis.com/tekton-releases/triggers/previous/v0.25.0/release.yaml`

### Tekton triggers interceptors release v0.25.0:

`kubectl apply -f https://storage.googleapis.com/tekton-releases/triggers/previous/v0.25.0/interceptors.yaml`

### Tekton dashboard release v0.40.1:

`kubectl apply -f https://storage.googleapis.com/tekton-releases/dashboard/previous/v0.40.1/release-full.yaml`

### Tekton dashboard ingress:

`kubectl apply -f apps/tekton/tekton-dashboard-ingress.yaml`
- Run the following command to check the pod status.

`kubectl get pods -n tekton-pipelines`

Sample output:

````
NAME                                                 READY   STATUS    RESTARTS   AGE
tekton-dashboard-64bf8f9dfd-tgv44                    1/1     Running   0          5d21h
tekton-events-controller-5fdf9cdd89-s4857            1/1     Running   0          5d21h
tekton-pipelines-controller-6d84b6bc96-mmpd9         1/1     Running   0          5d21h
tekton-pipelines-webhook-764d64c8bd-kfmlr            1/1     Running   0          5d21h
tekton-triggers-controller-6974c7d74b-vkqvz          1/1     Running   0          5d21h
tekton-triggers-core-interceptors-7cbd98668b-vgjd4   1/1     Running   0          5d21h
tekton-triggers-webhook-789d5f5bd4-xsnm7             1/1     Running   0          5d21h
````
## 4. Installing prometheus resources.
- Run the following command to install prometheus-server, node-exporter and kube-state-metrics.

`kubectl apply -f apps/prometheus/prometheus.yaml`

## 5. Installing grafana resources.
- Run the following command to install grafana.

`kubectl apply -f apps/grafana/grafana.yaml`

## 6. Creating a demo namespace.
- Run the following command to create a demo namespace.

`kubectl create namespace demopipelines`

## 7. Installing tekton demo pipelines.

- **(Optional)** If you want to run a simple CI/CD demo pipeline you have to create a private docker registry to push and pull docker images and the create the kubernetes secret using the following command:

`kubectl create secret docker-registry <secret-name> --docker-server=<your-registry-server> --docker-username=<your-name> --docker-password=<your-pword> --docker-email=<your-email>`

Then run the following command to replace the secret name on the service account:

`sed -i -e "s/SECRET_NAME/<your-secret-name>/g" DemoPipelines/resources/sa.yaml`

- Run the following commands to install demo pipelines with all the tasks associated.

`kubectl apply -f DemoPipelines/resources/`

`kubectl apply -f DemoPipelines/tasks/`

`kubectl apply -f DemoPipelines/pipeline/`

## 8. Running the tekton pipeline demo.
- To run the tekton demo pipelines run the following commands:

`kubectl create -f DemoPipelines/pipelinerun.yaml`

`kubectl create -f DemoPipelines/build-push-deploy-run.yaml`

- Use the tekton dashboard to check the pipeline result.

## 9. Accessing to tekton dashboard and grafana.
- Use the following URLs to access tekton dashboard and grafana:

### Tekton dashboard URL:

`https://tekton.<NGINX_INGRESS_IP>.nip.io`

### Grafana URL:

`https://grafana.<NGINX_INGRESS_IP>.nip.io`

user: admin

pass: admin
