
### Lab 2: Exploring Ad-Hoc Commands

In this lab, you will explore some common ad-hoc commands starting from memory details to copying files from one host to another.

Launch 2 more Ubuntu VM `managed-node1` and `managed-node2` and add ssh key to it.

### Step 1: Make Entry in the Host File

To run all the commands on the localhost as well, make the following entry:

```sh
sudo vi /etc/ansible/hosts
```
Add the `private ip` of the managed nodes to the `ansible-server`
```
PRIVATE_IP of managed-node1
PRIVATE_IP of managed-node2
localhost ansible_connection=local
```
Save and exit the file.

For enabling `PasswordLess authentication` follow the below steps:

On the ansible-server
```
ssh-keygen -t rsa -b 2048
```
Verify the  SSH key pair on your local machine:
```
ls -l ~/.ssh/id_rsa ~/.ssh/id_rsa.pub
```
Ensure the correct permissions for SSH files on your local machine:
```
chmod 600 ~/.ssh/id_rsa
chmod 644 ~/.ssh/id_rsa.pub
```
Copy the public key:
```
cat ~/.ssh/id_rsa.pub
```
Log into the remote server via another method (e.g., console access).
Add the public key to ~/.ssh/authorized_keys on the remote server:
```
echo "your_public_key_content" >> ~/.ssh/authorized_keys
```
Verify File Permissions

On the remote server, check:
```
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```
```
exit
```
Execute the below command on ansible-server to verify passwordless authentication has been enabled
```
ssh ubuntu@PUBLIC_IP of managed-node1
```
```
ssh ubuntu@PUBLIC_IP of managed-node2
```
```
ansible all -m ping
```


### Step 2: Get Memory Details
Run the following command to get the memory details:
```
ansible all -m command -a "free -h"
```

### Step 3: Create a New User on All Hosts
Use the below command to create a new user on all the hosts:
```
ansible all -m user -a "name=ansible-new home=/home/ansible-new" --become
```

### Step 4: Create a Directory in One of the Nodes
Run the following command to create a directory in one of the nodes:
```
ansible <Private_IP_node1> -m file -a "dest=/home/ansible-new mode=755 owner=ansible-new state=directory" --become
```

### Step 5: Create a File Inside the Directory
Use the following command to create a file inside the directory that we created in the previous step:
```
ansible <Private_IP_node1> -m file -a "dest=/home/ansible-new/demo.txt mode=600 state=touch" --become
```

### Step 6: Add Content to the File
Run the following command to add content to the file:
```
ansible <Private_IP_node1> -b -m lineinfile -a 'dest=/home/ansible-new/demo.txt line="This server is managed by Ansible"'
```

### Step 7: SSH to the Host to Check the File
Use the below command to SSH to the host where the file was created and check if the file is created or not. Enter exit to exit from the managed node:
```
ssh ec2-user@<Private_IP_node1>
```
```
sudo cat /home/ansible-new/demo.txt
```

### Step 8: Exit Back to the Control Node and Create a File
Exit back to the control node and create a file using touch:
```
exit
```
```
touch test.txt
```

### Step 9: Copy the Test File to the Directory
Use the following command to copy the test file to the directory that we created:
```
ansible <Private_IP_node1> -m copy -a "src=test.txt dest=/home/ansible-new/test" --become
```

### Step 10: Remove the Localhost Entry from the Hosts File
Now, remove the localhost entry from the hosts file:
```
sudo vi /etc/ansible/hosts
```
After removing the entry, the file should look as below:
```
10.142.0.9
10.142.0.10
```
