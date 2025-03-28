# How to run deployment 

- deployment create pod
```
kubectl apply -f indo_deployment.yaml
```
- Service connect apps in cluster
```
kubectl apply -f indo_service.yaml
```
- Ingress  set akses base on domain or subdomain
```
kubectl apply -f indo_ingress.yaml
```


# Need to be install
```pip install kubernetes```

```
from kubernetes import client, config, utils
import os

def trigger_k8s_deployment(yaml_file_path: str):
    """
    Trigger Kubernetes deployment using a YAML file.
    """
    try:
        # Load Kubernetes configuration (assumes kubeconfig is set up)
        config.load_kube_config()

        # Create a Kubernetes API client
        k8s_client = client.ApiClient()

        # Apply the YAML file
        with open(yaml_file_path, 'r') as yaml_file:
            utils.create_from_yaml(k8s_client, yaml_file)

        print(f"Successfully applied {yaml_file_path} to Kubernetes.")
    except Exception as e:
        print(f"Failed to apply {yaml_file_path}: {e}")
        raise
```


```
from repository.k8s_deployment import trigger_k8s_deployment

async def deploy_tenant_to_k8s(tenant_code: str):
    """
    Deploy tenant resources to Kubernetes using generated YAML files.
    """
    try:
        base_path = "./generated_services"
        deployment_file = os.path.join(base_path, f"{tenant_code}_deployment.yaml")
        service_file = os.path.join(base_path, f"{tenant_code}_service.yaml")
        ingress_file = os.path.join(base_path, f"{tenant_code}_ingress.yaml")

        # Trigger deployment
        trigger_k8s_deployment(deployment_file)
        trigger_k8s_deployment(service_file)
        trigger_k8s_deployment(ingress_file)

        return {"status": "success", "message": f"Deployment for tenant {tenant_code} triggered successfully."}
    except Exception as e:
        return {"status": "error", "message": str(e)}
```

```
from fastapi import FastAPI

app = FastAPI()

@app.post("/deploy/{tenant_code}")
async def deploy_tenant(tenant_code: str):
    result = await deploy_tenant_to_k8s(tenant_code)
    return result
```#   s i m p l e _ t e n a n t  
 