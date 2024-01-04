# Requirements
1. aws-cli v2
2. Kubectl
3. Helm

# Setting up
## Basic AWS
1. Create AWS EKS cluster and node group -> preferably 2-4 medium instances in the node group
2. Create Kubeconfig
    * aws eks --region <region> update-kubeconfig --name <cluster-name> 
## Deploying Dask to Kubernetes
1. Add the Dask Helm Repository
    * helm repo add dask https://helm.dask.org/
2. Install the Dask Helm Chart
    * helm upgrade my-dask dask/dask -f values.yaml
    * Specify any extra pip or condo packages as described on https://artifacthub.io/packages/helm/dask/dask#customizing-python-environment
3. Forward ports
    1. Execute this script (preferably bash)
```bash
  export DASK_SCHEDULER="127.0.0.1"
  export DASK_SCHEDULER_UI_IP="127.0.0.1"
  export DASK_SCHEDULER_PORT=8080
  export DASK_SCHEDULER_UI_PORT=8081
  kubectl port-forward --namespace default svc/my-dask-scheduler $DASK_SCHEDULER_PORT:8786 &
  kubectl port-forward --namespace default svc/my-dask-scheduler $DASK_SCHEDULER_UI_PORT:80 &

  export JUPYTER_NOTEBOOK_IP="127.0.0.1"
  export JUPYTER_NOTEBOOK_PORT=8082
  kubectl port-forward --namespace default svc/my-dask-jupyter $JUPYTER_NOTEBOOK_PORT:80 &

  echo tcp://$DASK_SCHEDULER:$DASK_SCHEDULER_PORT               -- Dask Client connection
  echo http://$DASK_SCHEDULER_UI_IP:$DASK_SCHEDULER_UI_PORT     -- Dask dashboard
  echo http://$JUPYTER_NOTEBOOK_IP:$JUPYTER_NOTEBOOK_PORT       -- Jupyter notebook%
```
