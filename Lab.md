### Once the VM is up & running, SSH into one of it and set the hostname as 'Control-Node'. 
```
sudo hostnamectl set-hostname Control-Node
```

### Install ansible
=====================
```
vi install-ansible.sh
```
```
sudo apt update && sudo apt upgrade -y
```
```
sudo apt install python3 python3-pip wget -y
```
```
sudo apt-add-repository --yes --update ppa:ansible/ansible
```
```
sudo apt install ansible -y
```
```
ansible --version
```

save the file :wq

And run the below command


```
sudo chmod +x install-ansible.sh
```
```
. ./install-ansible.sh
```


#### Task 1: Installing Terraform on Ubuntu 22.04 operating system

After VM is ready:

```
vi terraform.sh
```
```
sudo apt update
sudo apt install wget unzip -y
wget -O- https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update && sudo apt install terraform
terraform -v
```


save the file :wq

Provide the permission and run the script
```
sudo chmod +x terraform.sh 
```
```
. ./terraform.sh 
```

Create 2 more VM using terraform
===============================
1) Authenticate
```
gcloud auth application-default login  
```
and follow the instructions

2) create file
```
vi myfirstvm.tf 
```
```
provider "google" {
  project = "cloudthat-1709570355952" # change your project id
  region  = "us-central1"
  zone    = "us-central1-a"
}
```

3) create file
```
vi resorce.tf 
```
```
resource "google_compute_instance" "vm_instance" {
  count = 2
  name = "node${count.index}"
  machine_type = "e2-medium"
  boot_disk {
    initialize_params {
      image = "ubuntu-2204-lts"
    }
  }

  network_interface {
    # A default network is created for all GCP projects
    network = "default"  # change your default network name
    access_config {
    }
  }
}
```
save the file :wq-enter

Now run commands
```
terraform init
```
```
terraform fmt
```
```
terraform validate
```
```
terraform plan
```
```
terraform apply
```
```
terraform destroy
```

create  ssh key and copy the key into both the vm

```
vi ssh-key.sh
```
```
ssh-keygen -t rsa -b 2048
sudo chmod 600 ~/.ssh/id_rsa
sudo chmod 644 ~/.ssh/id_rsa.pub
cat ~/.ssh/id_rsa.pub
```

save the file :wq
```
sudo chmod +x ssh-key.sh
. ./ssh-key.sh
```



Login node0 and copy the key OR Stop the VM and Edit it add the key then start again
```
sudo vi  ~/.ssh/authorized_keys
```
add key
```
cat ~/.ssh/authorized_keys
```
# login node1 and copy the key
```
sudo vi ~/.ssh/authorized_keys
```
add key
```
cat ~/.ssh/authorized_keys
```


verify
---------------------
```
ssh username@privateip
```
```
ssh username@privateip
```

add private ip of both the nodes in the inventory file as below
```
sudo vi /etc/ansible/hosts
```

verify the ip
```
head -n 3 /etc/ansible/hosts 
```
This is the default ansible 'hosts' file.
10.142.0.3
10.142.0.2

Now check the run ping command to check the connectivity to the both the nodes

```
ansible all -m ping 
```









