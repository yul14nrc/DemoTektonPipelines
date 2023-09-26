# Running Tekton pipelines demo in k8s
## 1. Prerequisites
- K8s cluster to install the tekton pipelines instance.
## 2. Installing tekton pipelines resources
- Run the following commands to install the latest tekton pipelines resources.

`kubectl apply --filename https://storage.googleapis.com/tekton-releases/pipeline/latest/release.yaml`

`kubectl apply --filename https://storage.googleapis.com/tekton-releases/triggers/latest/release.yaml`

`kubectl apply --filename https://storage.googleapis.com/tekton-releases/triggers/latest/interceptors.yaml`

`kubectl apply --filename https://storage.googleapis.com/tekton-releases/dashboard/latest/release.yaml`

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
## 3. Creating a demo namespace
- Run the following command to create a demo namespace.

`kubectl create namespace demopipelines`

## 4. Installing tekton pipeline demo
- Run the following commands to install a demo pipeline with all the tasks associated.

`kubectl apply -f DemoPipelines/tasks/`

`kubectl apply -f DemoPipelines/pipelines/`

## 5. Exposing the tekton dashboard
- In order to check the pipelinerun status expose the tekton dashboard to a localhost port using the following command:

`kubectl port-forward svc/tekton-dashboard 9097:9097 -n tekton-pipelines`

- Open a browser a navigate to https://localhost:9097 to open the tekton dashboard.
## 6. Running the tekton pipeline demo
- To run the tekton pipeline demo run the following command:

`kubectl create -f DemoPipelines/pipelines/pipelinerun.yaml`

- Use the tekton dashboard to check the pipeline result.
