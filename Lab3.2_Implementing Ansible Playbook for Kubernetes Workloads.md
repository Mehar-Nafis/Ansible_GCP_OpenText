## Implementing Ansible Playbook for Kubernetes Workloads

### Prerequisites

Before you begin, ensure you have the following prerequisites:

1. Ansible installed on your control machine.
2. GKE CLuster.
3. Install Required Packages and Authenticate 
```
snap install google-cloud-cli --classic
```
```
gcloud init
```
```
gcloud auth application-default login
```
4. Connecting to the Cluster
```
sudo apt-get install kubectl
```
```
sudo apt-get install gke-gcloud-auth-plugin
```
```
sudo apt-get install google-cloud-cli-gke-gcloud-auth-plugin
```
```
gcloud container clusters get-credentials ninad-gke-cluster --zone us-central1-c --project deloitte-team2
```
```
kubectl get nodes
```

### Steps
1. Download the repo
```sh
sudo git clone https://github.com/Mehar-Nafis/Managing-GKE-Workloads-using-Ansible-Playbook.git
```

2. Create a `yml` file in the `ansible/inventory` folder to allow Ansible to interact with your `GCP` environment.
```sh
cd Managing-GKE-Workloads-using-Ansible-Playbook/ && cd ansible/
```
```sh
sudo mkdir  inventory && cd inventory/
```
```
sudo vi sudo vi gcp.yml
```
```yaml
all:
  vars:
    # use this section to enter GCP related information
    gcloud_sa_path: "~/gcp-credentials/service-account.json"
    credentials_file: "{{ lookup('env','HOME') }}/{{ gcloud_sa_path }}"
    
    # use the section below to enter k8s namespaces to manage
    # this namespace is used in the Deploying an Application section
    namespace: nginx
```

3. Deploying an application

Execute the following command to deploy the `Nginx` web-server:

```bash
ansible-playbook ansible/deploy-app-k8s.yml -i ansible/inventory/<your-inventory-filename>
```

4. Accessing the Nginx

Execute the following commands and then access the `Nginx` using this [URL](http://127.0.0.1:8080).

```bash
export POD_NAME=$(kubectl get pods --namespace nginx -l "app=nginx" -o jsonpath="{.items[0].metadata.name}")
kubectl --namespace nginx port-forward $POD_NAME 8080:80 
```

5. Cleaning up

Execute the following command to destroy the `Kubernetes` cluster:
```
`ansible-playbook ansible/destroy-k8s.yml -i ansible/inventory/<your-inventory-filename>`
```


