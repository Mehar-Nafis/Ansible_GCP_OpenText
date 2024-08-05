## Lab 6: Working with Ansible Functions

### Task 1: Loops with Ansible Playbook

1. Make the present working directory as labs

    ```sh
    cd /home/ubuntu/labs && sudo mkdir lab6 && cd lab6
    ```

2. Create and edit `looplab.yml` to define the loop to add a list of users

    ```sh
    sudo vi looplab.yml
    ```

3. Enter the contents of the file as shown below:

    ```yaml
    ---
    - hosts: all
      become: yes
      connection: ssh
      tasks:
        - name: Adding a number of users
          user:
            name: "{{ item }}"
            state: present
          with_items:
            - userX
            - userY
            - userZ
    ```

4. Run the Ansible playbook

    ```sh
    ansible-playbook looplab.yml
    ```

5. Verify if the users mentioned in the list were added by using an Ansible ad-hoc command

    ```sh
    ansible all -a "tail -n 3 /etc/passwd"
    ```


### Task 2: Tags with Ansible Playbooks

1. Create and edit `tagslabs.yml` in the same `labs` directory

    ```sh
    sudo vi tagslabs.yml
    ```

2. Enter the contents of the file as shown below

    ```yaml
    ---
    - hosts: all
      become: yes
      connection: ssh
      gather_facts: no
      tasks:
        - name: Update apt cache
          apt:
            update_cache: yes
            cache_valid_time: 3600  # Cache validity time in seconds
          tags:
            - packages
    
        - name: Install telnet
          apt:
            name: telnet
            state: latest
          tags:
            - packages

    ```

3. Run the playbook

    ```sh
    ansible-playbook tagslabs.yml
    ```

4. Run the playbook again, this time using tags. Notice that only the tasks associated with the mentioned tags are running

    ```sh
    ansible-playbook -t "logging" tagslabs.yml
    ```

    ```sh
    ansible-playbook -t "packages" tagslabs.yml
    ```

### Task 3: Prompts with Ansible Playbooks

1. Create and edit `promptlab.yml` in the same directory

    ```sh
    sudo vi promptlab.yml
    ```

2. Enter the contents of the file as shown below:

    ```yaml
    ---
    - hosts: all
      become: yes
      connection: ssh
      vars_prompt:
        - name: pkginstall
          prompt: Which package do you want to install?
          default: telnet
          private: no
      tasks:
        - name: Install the package specified
          apt:
            pkg: "{{ pkginstall }}"
            state: latest
    ```

3. When the playbook is run, the user will be presented with a prompt. The package entered will be installed

    ```sh
    ansible-playbook promptlab.yml
    ```

4. Verify if the specified package apache is installed. SSH into one of the machines and verify using the command shown below:

    ```sh
    ssh ubuntu@<managed_node_private_ip>
    ```

    ```sh
    dpkg -l | grep apache
    ```

    Similarly, installation on the other client machine can be verified as well.

### Task 4: Until with Ansible Playbooks

1. Create and edit `untillab.yml` in the same `labs` directory

    ```sh
    sudo vi untillab.yml
    ```

2. Enter the contents of the file, as shown below. It checks if `apacche` service is running three times with a 5-second delay

    ```yaml
    ---
    - hosts: all
      become: yes
      connection: ssh
      tasks:
        - name: Install Apache Web Server
          apt:
            name: apache2
            state: latest
        - name: Verify Status of Service
          shell: systemctl status apache2
          register: result
          until: result.stdout.find("active (running)") != -1
          retries: 3
          delay: 5
    ```

3. Run the playbook. Notice that the output of the command is shown along with the Ansible until command output

    ```sh
    sudo ansible-playbook untillab.yml
    ```

### Task 5: Run Once with Ansible Playbook

1. Create and edit `rolab.yml` in the same `labs` directory

    ```sh
    sudo vi rolab.yml
    ```

2. Enter the contents of the file, as shown below. It will record the uptime of the first machine at `/home/ec2-user/uptime`

    ```yaml
    ---
    - hosts: all
      become: yes
      user: ubuntu
      connection: ssh
      gather_facts: no
      tasks:
        - name: Recording uptime on all machines
          raw: /usr/bin/uptime >> /home/ubuntu/uptime
          run_once: true
    ```

3. Run the playbook

    ```sh
    sudo ansible-playbook rolab.yml
    ```

4. Verify if the file exists and has the right contents on either of the client machines

    ```sh
    ansible <managed_node_private_ip> -a "cat /home/ubuntu/uptime"
    ```

### Task 6: Blocks with Ansible Playbook

1. Create and edit `blklab.yml` in the same `labs` directory

    ```sh
    vi blklab.yml
    ```

2. **Enter the contents of the file, as shown below. Notice that the `web_package` variable is an invalid package. Due to the invalid package in a block, tasks under rescue will run**

    ```yaml
    ---
    - hosts: all
      become: yes
      user: ec2-user
      connection: ssh
      gather_facts: no
      tasks:
        - block:
            - name: Install {{ web_package }} package
              yum:
                name: "{{ web_package }}"
                state: latest
          rescue:
            - name: Install {{ db_package }} package
              yum:
                name: "{{ db_package }}"
                state: latest
          always:
            - name: Start {{ db_service }} service
              service:
                name: "{{ db_service }}"
                state: started
      vars:
        web_package: http
        db_package: mariadb-server
        db_service: mariadb
    ```

3. **Run the Playbook and see that Block tasks fail and that Rescue tasks are running due to the failure of block tasks. The Always tasks run independently**

    ```sh
    ansible-playbook blklab.yml
    ```

4. **Now fix the package name in the Playbook and run the Playbook again**

    ```sh
    vi blklab.yml
    ```

    ```yaml
    ---
    - hosts: all
      become: yes
      user: ec2-user
      connection: ssh
      gather_facts: no
      tasks:
        - block:
            - name: Install {{ web_package }} package
              yum:
                name: "{{ web_package }}"
                state: latest
          rescue:
            - name: Install {{ db_package }} package
              yum:
                name: "{{ db_package }}"
                state: latest
          always:
            - name: Start {{ db_service }} service
              service:
                name: "{{ db_service }}"
                state: started
      vars:
        web_package: httpd
        db_package: mariadb-server
        db_service: mariadb
    ```

    ```sh
    ansible-playbook blklab.yml
    ```

5. **Notice that the tasks under rescue are not running as block tasks ran successfully.**
