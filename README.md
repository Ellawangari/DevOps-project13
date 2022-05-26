# Project 13 Ansible Dynamic Assignments (Include) and Community Roles

# Step 1: Introducing Dynamic Assignment Into our Structure
 
- Created a github branch dynamic-assignments and created two new files called env-vars.yml and default.yml
- The finally layout after creating the required roles and folders was to have such a structure.
- ```├── dynamic-assignments
    │   └── env-vars.yml
    ├── env-vars
    └── dev.yml
    └── stage.yml
    └── uat.yml
    └── prod.yml
   ├── inventory
    └── dev
    └── stage
    └── uat
    └── prod
   ├── playbooks
    └── site.yml
   └── static-assignments
    └── common.yml
    └── webservers.yml
    ```
    
 - ![alt text](https://github.com/Ellawangari/DevOps-project13/blob/main/imgs/1.PNG)
 - Pasted the instruction below into the env-vars.yml file.
 ```---
    - name: collate variables from env specific file, if it exists
      hosts: all
       tasks:
    - name: looping through list of available files
      include_vars: "{{ item }}"
      with_first_found:
        - files:
            - dev.yml
            - stage.yml
            - prod.yml
            - uat.yml
          paths:
            - "{{ playbook_dir }}/../env-vars"
      tags:
        - always 
 ```
      
      
      
  # Step 2: Community roles
  - Installed Mysql role by geerlingguy (can be found at https://galaxy.ansible.com/geerlingguy/mysql)
  -  Used the README.md file and configure the role as needed. on the `defaults/task.yaml file`
  - ![alt text](https://github.com/Ellawangari/DevOps-project13/blob/main/imgs/2.PNG)
     - ![alt text](https://github.com/Ellawangari/DevOps-project13/blob/main/imgs/3.PNG)    
     
     - Uploaded my changes to github
     - ![alt text](https://github.com/Ellawangari/DevOps-project13/blob/main/imgs/4.PNG)
     
     
    # Step 3: Load Balancer roles
     - Created  roles for both nginx and apache using `https://galaxy.ansible.com/geerlingguy/nginx` and `https://galaxy.ansible.com/geerlingguy/apache`
     - ![alt text](https://github.com/Ellawangari/DevOps-project13/blob/main/imgs/5.PNG)
     - Since one cannot use both Nginx and Apache load balancer, you need to add a condition to enable either one - this is where you can make use of variables
      - ![alt text](https://github.com/Ellawangari/DevOps-project13/blob/main/imgs/6.PNG)
     - Declared a variable in defaults/main.yml file inside the Nginx and Apache roles. Name each variables enable_nginx_lb and enable_apache_lb respectively.
    -  Set both values to false  enable_nginx_lb: false and enable_apache_lb: false.
    - Update the neccesary files like below
    ```
    loadbalancers.yml file:
    ```
    - hosts: lb
      roles:
        - { role: nginx, when: enable_nginx_lb and load_balancer_is_required }
        - { role: apache, when: enable_apache_lb and load_balancer_is_required }
    ```
    sites.yml file:
    ```
    ---
    - import_playbook: ../static-assignments/common.yml
    - import_playbook: ../static-assignments/webservers.yml
    - import_playbook: ../static-assignments/mysql.yml

    - hosts: lb
      tasks:
        - name: Load dynamic vars
          include_tasks: ../dynamic-assignments/env-vars.yml

    - import_playbook: ../static-assignments/loadbalancers.yml```
    
    I activated load balancer, and enable nginx (for example) by setting these in the respective environment’s env-vars file.
    ```
    enable_nginx_lb: true
    load_balancer_is_required: true
    ````
    - ![alt text](https://github.com/Ellawangari/DevOps-project13/blob/main/imgs/10.PNG)
    then executed the the ansible playbook to execute the roles on the respective servers
    - ![alt text](https://github.com/Ellawangari/DevOps-project13/blob/main/imgs/7.PNG)
    - ![alt text](https://github.com/Ellawangari/DevOps-project13/blob/main/imgs/8.PNG)
    - ![alt text](https://github.com/Ellawangari/DevOps-project13/blob/main/imgs/9.PNG)
    - ![alt text](https://github.com/Ellawangari/DevOps-project13/blob/main/imgs/11.PNG)
    
