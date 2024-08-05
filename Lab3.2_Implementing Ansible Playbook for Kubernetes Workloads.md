## Implementing Ansible Playbook for Kubernetes Workloads

### Prerequisites

Before you begin, ensure you have the following prerequisites:

1. Ansible installed on your control machine.
2. Access to a Kubernetes cluster.
3. The `kubernetes.core` collection installed. You can install it using:
   ```bash
   ansible-galaxy collection install kubernetes.core
   ```
### Steps
A typical Ansible playbook for Kubernetes workloads includes tasks for:
Setting up the Kubernetes environment.
Deploying applications to the cluster.
Managing Kubernetes resources such as pods, services, and deployments.

```sh
sudo vi k8s_playbook.yml
```
```yaml
---
- name: Deploy applications to Kubernetes
  hosts: localhost
  become: yes
  tasks:
    - name: Ensure kubectl is installed
      package:
        name: kubectl
        state: present

    - name: Create a Kubernetes namespace
      kubernetes.core.k8s:
        state: present
        definition:
          apiVersion: v1
          kind: Namespace
          metadata:
            name: my-namespace

    - name: Deploy an application
      kubernetes.core.k8s:
        state: present
        definition:
          apiVersion: apps/v1
          kind: Deployment
          metadata:
            name: nginx-deployment
            namespace: my-namespace
          spec:
            replicas: 3
            selector:
              matchLabels:
                app: nginx
            template:
              metadata:
                labels:
                  app: nginx
              spec:
                containers:
                - name: nginx
                  image: nginx:1.19.4
                  ports:
                  - containerPort: 80

    - name: Expose the application
      kubernetes.core.k8s:
        state: present
        definition:
          apiVersion: v1
          kind: Service
          metadata:
            name: nginx-service
            namespace: my-namespace
          spec:
            selector:
              app: nginx
            ports:
            - protocol: TCP
              port: 80
              targetPort: 80
            type: LoadBalancer
```

To run the playbook, use the following command:
```
ansible-playbook k8s_playbook.yml
```
This command will execute the tasks defined in the playbook, setting up your Kubernetes environment and deploying the application.

