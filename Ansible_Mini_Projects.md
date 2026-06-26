*Ansible Mini Projects* 
**Project 1: Install Java 21 and Jenkins using Ansible**  
**Objective**  
Automate the installation and configuration of Java 21 and Jenkins on  
   
 Ubuntu servers.  
**Installation Playbook**  
---  
 - name: Install Java 21 and Jenkins  
   hosts: all  
   become: true  
   
   tasks:  
     - name: Update apt cache  
       ansible.builtin.apt:  
         update_cache: true  
   
     - name: Install fontconfig and OpenJDK 21  
       ansible.builtin.apt:  
         name:  
           - fontconfig  
           - openjdk-21-jre  
         state: present  
   
     - name: Create apt keyrings directory  
       ansible.builtin.file:  
         path: /etc/apt/keyrings  
         state: directory  
         mode: '0755'  
   
     - name: Download Jenkins GPG key  
       ansible.builtin.get_url:  
         url: https://pkg.jenkins.io/debian-stable/jenkins.io-2026.key  
         dest: /etc/apt/keyrings/jenkins-keyring.asc  
         mode: '0644'  
   
     - name: Add Jenkins repository  
       ansible.builtin.copy:  
         dest: /etc/apt/sources.list.d/jenkins.list  
         content: |  
           deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/  
   
     - name: Update apt cache  
       ansible.builtin.apt:  
         update_cache: true  
   
     - name: Install Jenkins  
       ansible.builtin.apt:  
         name: jenkins  
         state: present  
   
     - name: Start Jenkins  
       ansible.builtin.systemd:  
         name: jenkins  
         state: started  
         enabled: true  
   
**Uninstall Playbook**  
---  
 - name: Uninstall Java 21 and Jenkins  
   hosts: all  
   become: true  
   
   tasks:  
     - name: Update apt cache  
       ansible.builtin.apt:  
         update_cache: true  
   
     - name: Uninstall fontconfig and OpenJDK 21  
       ansible.builtin.apt:  
         name:  
           - fontconfig  
           - openjdk-21-jre  
         state: absent  
   
     - name: Uninstall Jenkins  
       ansible.builtin.apt:  
         name: jenkins  
         state: absent  
   
![](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAnEAAAACCAYAAAA3pIp+AAAABmJLR0QA/wD/AP+gvaeTAAAACXBIWXMAAA7EAAAOxAGVKw4bAAAANUlEQVR4nO3OMQ2AABAAsSNhwgJWEPcbJpnRgQU2QtIq6DIze3UGAMBf3Gu1VcfXEwAAXrseaIkEMIPgIvAAAAAASUVORK5CYII=)  
**Project 2: Deploy Docker Compose using Ansible**  
**docker-compose.yml**  
services:  
   web:  
     image: nginx:latest  
     container_name: nginx-web  
     ports:  
       - "8090:80"  
     restart: always  
   
**Deployment Playbook**  
---  
 - name: Deploy Docker Compose Application  
   hosts: all  
   become: true  
   
   tasks:  
     - name: Update apt cache  
       ansible.builtin.apt:  
         update_cache: true  
   
     - name: Install Docker  
       ansible.builtin.apt:  
         name:  
           - docker.io  
           - docker-compose-v2  
         state: present  
   
     - name: Start Docker  
       ansible.builtin.systemd:  
         name: docker  
         state: started  
         enabled: true  
   
     - name: Create application directory  
       ansible.builtin.file:  
         path: /opt/nginx-app  
         state: directory  
   
     - name: Copy docker compose file  
       ansible.builtin.copy:  
         src: docker-compose.yml  
         dest: /opt/nginx-app/docker-compose.yml  
   
     - name: Deploy application  
       ansible.builtin.command:  
         cmd: docker compose up -d  
         chdir: /opt/nginx-app  
   
**Cleanup / Uninstall Playbook**  
---  
 - name: Delete Docker Compose App  
   hosts: all  
   become: true  
   
   tasks:  
     - name: Update apt cache  
       ansible.builtin.apt:  
         update_cache: true  
   
     - name: Stop and remove Docker Compose application  
       ansible.builtin.command:  
         cmd: docker compose down  
         chdir: /opt/nginx-app  
       ignore_errors: true  
   
     - name: Remove application directory  
       ansible.builtin.file:  
         path: /opt/nginx-app  
         state: absent  
   
     - name: Uninstall packages  
       ansible.builtin.apt:  
         name:  
           - docker.io  
           - docker-compose-v2  
         state: absent  
   
**Common Commands**  
ansible-playbook -i inventroy.ini jenkins.yml --ask-become-pass  
 ansible-playbook -i inventroy.ini uninstall-jenkins.yml --ask-become-pass  
   
 ansible-playbook -i inventroy.ini deploy-docker-compose.yml --ask-become-pass  
 ansible-playbook -i inventroy.ini delete-docker-compose.yml --ask-become-pass  
   
**Verification**  
java -version  
 systemctl status jenkins  
 docker ps  
 docker compose ls  
 curl http://localhost:8090  
   
