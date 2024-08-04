## Lab 1: Installation and Configuration of Ansible

### Task 1: Launching an Instance in GCP

1. Go to the GCP Console and select Compute Engine.
2. Select VM Instances and click on CREATE INSTANCE.
3. Enter the name of the instance `ansible-server` and select the Zone in which the instance is going to launch.
4. Select machine type. In the boot disk section, click on Change and select Ubuntu 24.04.
5. Click on create and launch the instance.
6. SSH into the machine

### Task 2: Execute the Following Commands for Installing the Ansible Server

1. Set hostname as “ansible-workstation” by using the below command and restart the PuTTY session:
    ```bash
    $ sudo hostnamectl set-hostname ansible-workstation
    ```

2. Check the IP is changed to “ansible-workstation” hostname and run the following command to update:
    ```bash
    $ sudo yum clean all
    $ sudo yum update all
    ```

3. Install Python 3.9, python3-pip, and wget which are compatible with Ansible using the below command:
    ```bash
    $ sudo yum install python3.9 python3-pip wget -y
    ```

4. Use the following command to download the `epel-release` package:
    ```bash
    $ wget https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
    ```

5. Use the following command to install the `epel-release` package:
    ```bash
    $ sudo yum install epel-release-latest-8.noarch.rpm -y
    ```

6. Now install Ansible by using the following commands:
    ```bash
    $ pip3 install ansible --user
    $ sudo yum install ansible -y
    ```

7. Check the version of Ansible using the below command:
    ```bash
    $ ansible --version
    ```

8. Make a note of the returned private IP of Node1 and Node2 from the GCP console.

9. Now go to the hosts file and make an entry of managed nodes using the below command:
    ```bash
    $ sudo vi /etc/ansible/hosts
    ```

10. Add the private IPs of the managed nodes by going to GCP Console and write them to the hosts file. The file will look as below:

11. By listing all the hosts, check if it gives you 2 IPs that we added.

12. SSH into each managed node, type yes and exit back to the control node for SSH host key verification:
    ```bash
    $ ssh ec2-user@<managed_node1_private_ip>
    $ ssh ec2-user@<managed_node2_private_ip>
    ```

13. Try pinging both the nodes if you can successfully do that using the below command from the control node:
    ```bash
    $ ansible all -m ping
    ```
