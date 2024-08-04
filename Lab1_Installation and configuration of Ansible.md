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
    sudo apt install python3 python3-pip wget -y
    ```

3. Use the following command to add the Ansible PPA:
    ```bash
    sudo apt-add-repository --yes --update ppa:ansible/ansible
    ```

4. Now install Ansible by using the following command:
    ```bash
    sudo apt install ansible -y
    ```

5. Check the version of Ansible using the below command:
    ```bash
    ansible --version
    ```
