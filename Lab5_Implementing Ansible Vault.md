## Lab 5: Implementing Ansible Vault

In this lab, we will create a sample playbook and perform vault operations using Ansible Vault.

### Steps

1. Navigate to the `labs` directory and create a playbook named `implement-vault.yml`:

    ```sh
    cd /home/ubuntu/labs
    ```
    ```sh
    sudo mkdir lab5 && cd lab5
    ```
    ```sh
    sudo vi implement-vault.yml
    ```

2. Add the following content to the playbook:

    ```yaml
    ---
    - hosts: all

      tasks: 
        - file: 
            path: /home/ubuntu/labs/lab5/test.conf 
            state: touch 
            owner: ubuntu 
            group: ubuntu 
            mode: 0644
    ```

3. Encrypt the playbook using the vault utility:

    ```sh
    sudo ansible-vault encrypt implement-vault.yml
    ```

4. Provide a password of your choice when prompted, and confirm it.

5. To view the encrypted playbook, use the following command and provide the password:

    ```sh
    sudo ansible-vault view implement-vault.yml
    ```

6. Execute the encrypted playbook by using the following command and providing the vault password:

    ```sh
    sudo ansible-playbook --ask-vault-pass implement-vault.yml
    ```

7. Edit the playbook with the vault password:

    ```sh
    sudo ansible-vault edit implement-vault.yml
    ```

8. Replace the `mode` line with the following and save the playbook:

    ```yaml
    mode: "u=rw,g=r,o=r"
    ```

9. Execute the playbook again with the same command:

    ```sh
    ansible-playbook --ask-vault-pass implement-vault.yml
    ```

10. To change the vault password, use the following command and provide both the old and new passwords:

    ```sh
    ansible-vault rekey implement-vault.yml
    ```

11. To view the encrypted file's content, use the `cat` command:

    ```sh
    cat implement-vault.yml
    ```

12. Decrypt the file to view its content in plain text:

    ```sh
    ansible-vault decrypt implement-vault.yml
    cat implement-vault.yml
    ```

By following these steps, you have successfully created, encrypted, viewed, executed, edited, rekeyed, and decrypted an Ansible playbook using Ansible Vault.
