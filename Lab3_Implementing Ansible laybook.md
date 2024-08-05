## Lab 3: Implementing Ansible Playbook

In this lab, we will be creating our first playbook to install and configure the Apache web server and will execute the playbook.

### Steps

1. **Create a new directory `labs` in the `/home/ec2-user` directory**

    ```sh
    $ sudo mkdir /home/ec2-user/labs
    $ cd /home/ec2-user/labs
    ```

2. **Create a new file `install-apache-pb.yml` (the playbook)**

    ```sh
    $ sudo vi install-apache-pb.yml
    ```

3. **Add the below code to the newly created playbook:**

    ```yaml
    ---
    - name: This play will install apache web servers on all the hosts
      hosts: all
      become: yes
      tasks:
        - name: Task1 will install httpd using yum
          yum:
            name: httpd
            update_cache: yes
            state: latest
        - name: Task2 will upload custom index.html into all hosts
          copy:
            src: "index.html"
            dest: "/var/www/html/index.html"
        - name: Task3 will setup attributes for file
          file:
            path: /var/www/html/index.html
            owner: apache
            group: apache
            mode: 0644
        - name: Task4 will start the httpd
          service:
            name: httpd
            state: started
    ```

4. **Save and exit the file after adding the content**

5. **Create a simple `index.html` file in the same `labs` directory**

    ```sh
    $ sudo vi index.html
    ```

6. **Add the below code to the `index.html` file**

    ```html
    <html>
      <body>
        <h1>Welcome to CloudThat</h1>
        <img src="https://d3ffutjd2e35ce.cloudfront.net/assets/logo1.png">
      </body>
    </html>
    ```

7. **Execute the playbook using the `ansible-playbook` command**

    ```sh
    $ ansible-playbook install-apache-pb.yml
    ```

8. **Verify the setup by doing a curl on both managed nodes to check the `index.html`**

    ```sh
    $ curl <private_ip_of_node1>
    $ curl <private_ip_of_node2>
    ```
