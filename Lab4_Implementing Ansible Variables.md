## Lab 3: Implementing Ansible Variables

In this lab, we will learn how to use variables in Ansible inside the playbook.

### Task 1: Configuring Packages in Ansible Using Variables

1. Create and change into the "file" directory inside the "labs" directory**

    ```sh
    cd /home/ubuntu/labs/
    ```
    ```sh
    sudo mkdir lab4 && cd lab4
    ```
   
2. Create a new playbook for implementing variables**

    ```sh
    sudo vi implement-vars.yml
    ```

3. Add the following content to the `implement-vars.yml` file**

    ```yaml
    ---
    - hosts: '{{ hostname }}'
      become: yes
      vars:
        hostname: all
        package1: apache2
        destination: /var/www/html/index.html
        source: /home/ec2-user/labs/index.html
      tasks:
        - name: Install defined package
          apt:
            name: '{{ package1 }}'
            update_cache: yes
            state: latest
        - name: Start desired service
          service:
            name: '{{ package1 }}'
            state: started
        - name: Copy required index.html to the document folder for apache2
          copy:
            src: '{{ source }}'
            dest: '{{ destination }}'
    ```

4. Create a file called `index.html`**

    ```sh
    sudo vi index.html
    ```
    ```html
    <html>
      <body>
        <h1>Welcome to CloudThat</h1>
        <img src="https://d3ffutjd2e35ce.cloudfront.net/assets/logo1.png">
      </body>
    </html>
    ```

5. Change to the `file` directory and run the playbook to see the implementation of variables**

    ```sh
    ansible-playbook implement-vars.yml
    ```

6. Use the public IP of any of the Ansible managed instances to view the webpage**


## Task 2: Implementing Ansible Variables Using `extra-vars` Option

1. **Create a file `index1.html` and copy the following contents into that file**

    ```sh
    sudo vi index1.html
    ```

    ```html
    <html>
      <body>
        <h1>This is the alternate Home Page</h1>
        <img src="https://d3ffutjd2e35ce.cloudfront.net/assets/logo1.png">
      </body>
    </html>
    ```

2. **Implement the playbook using the following command**

    ```sh
    $ ansible-playbook implement-vars.yml --extra-vars "source=/home/ec2-user/labs/file/index1.html"
    ```

3. **Use the public IP of any of the Ansible managed instances to view the webpage**

## Task 3: Configuring Variables as a Separate File and Implementing Ansible Playbook

1. **Create a file `myvariables.yml` and add your variables**

    ```sh
    $ sudo vi myvariables.yml
    ```

    ```yaml
    ---
    hostname: all
    package1: httpd
    destination: /var/www/html/index.html
    source: /home/ec2-user/labs/index.html
    ```

2. **Modify the `implement-vars.yml` with the below-mentioned changes**

    ```sh
    $ sudo vi implement-vars.yml
    ```

    ```yaml
    ---
    - hosts: '{{ hostname }}'
      become: yes
      vars_files:
        - myvariables.yml
      tasks:
        - name: Install defined package
          yum:
            name: '{{ package1 }}'
            update_cache: yes
            state: latest
        - name: Start desired service
          service:
            name: '{{ package1 }}'
            state: started
        - name: Copy required index.html to the document folder for httpd
          copy:
            src: '{{ source }}'
            dest: '{{ destination }}'
    ```

3. **Implement the Ansible playbook with the following command**

    ```sh
    $ ansible-playbook implement-vars.yml
    ```

4. **Use the public IP of any of the Ansible managed instances to view the webpage**

## Lab 6 Task Inclusion

1. **Create a file named `second.yaml` with the below contents which has a list of tasks to install and start httpd (Apache) service**

    ```sh
    $ sudo vi second.yaml
    ```

    ```yaml
    ---
    - name: Install the httpd package
      yum:
        name: httpd
        state: latest
        update_cache: yes

    - name: Start the httpd service
      service:
        name: httpd
        state: started
        enabled: yes
    ```

2. **Create another playbook named `first.yaml`, which includes the earlier created task (`second.yaml`)**

    ```sh
    $ sudo vi first.yaml
    ```

    ```yaml
    ---
    - hosts: localhost
      gather_facts: no
      become: yes
      tasks:
        - name: Install common packages
          yum:
            name: [wget, curl]
            state: present

        - name: Include task for httpd installation
          include_tasks: second.yaml
    ```

3. **Execute the playbook named `first.yaml` using the below command**

    ```sh
    $ ansible-playbook first.yaml
    ```

    You will notice in the output that it is also executing the plays/tasks in the `second.yaml`.

4. **Create another playbook named `third.yaml` as below**

    ```sh
    $ sudo vi third.yaml
    ```

    ```yaml
    ---
    - hosts: localhost
      gather_facts: no
      become: yes
      tasks:
        - name: Install common packages
          yum:
            name: [wget, curl]
            state: present
          register: out

        - name: List result of previous task
          debug:
            msg: "{{ out.rc }}"

        - name: Include task for httpd installation
          include_tasks: second.yaml
          when: out.rc == 0
    ```

5. **Execute the playbook using the below command**

    ```sh
    $ ansible-playbook third.yaml
    ```

Save the above content into a file with a `.md` extension, for example, `Lab3_Implementing_Ansible_Variables.md`.
