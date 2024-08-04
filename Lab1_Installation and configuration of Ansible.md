## Lab 1: Installation and Configuration of Ansible

### Task 1: Launching an Instance in GCP

1. Go to the GCP Console and select Compute Engine.
2. Select VM Instances and click on CREATE INSTANCE.
3. Enter the name of the instance `ansible-server` and select the Zone in which the instance is going to launch.
4. Select machine type. In the boot disk section, click on Change and select Ubuntu 24.04.
5. Click on create and launch the instance.
6. SSH into the machine

### Task 2: Execute the Following Commands for Installing the Ansible Server

1. Check the IP is changed to “ansible-workstation” hostname and run the following command to update:
    ```bash
    sudo apt update && sudo apt upgrade -y
    ```

2. Install Python 3.9, python3-pip, and wget which are compatible with Ansible using the below command:
    ```bash
    $ sudo apt install python3.9 python3-pip wget -y
    ```

3. Use the following command to add the Ansible PPA:
    ```bash
    $ sudo apt-add-repository --yes --update ppa:ansible/ansible
    ```

4. Now install Ansible by using the following command:
    ```bash
    $ sudo apt install ansible -y
    ```

5. Check the version of Ansible using the below command:
    ```bash
    $ ansible --version
    ```

6. Make a note of the returned private IP of Node1 and Node2 from the GCP console.

7. Now go to the hosts file and make an entry of managed nodes using the below command:
    ```bash
    $ sudo vi /etc/ansible/hosts
    ```

8. Add the private IPs of the managed nodes by going to GCP Console and write them to the hosts file. The file will look as below:

9. By listing all the hosts, check if it gives you 2 IPs that we added.

10. SSH into each managed node, type yes and exit back to the control node for SSH host key verification:
    ```bash
    $ ssh ec2-user@<managed_node1_private_ip>
    $ ssh ec2-user@<managed_node2_private_ip>
    ```

11. Try pinging both the nodes if you can successfully do that using the below command from the control node:
    ```bash
    $ ansible all -m ping
    ```
