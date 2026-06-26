**🚀 Ansible Mini Projects Handbook**  
***Audience:*** * DevOps Engineers, Cloud Engineers, SREs, and Interview*  
 *  
 Preparation*  
 *  
 * ***OS:*** * Ubuntu 24.04+*  
 *  
 * ***Automation Tool:*** * Ansible*  
![](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAnEAAAACCAYAAAA3pIp+AAAABmJLR0QA/wD/AP+gvaeTAAAACXBIWXMAAA7EAAAOxAGVKw4bAAAANklEQVR4nO3OMQ2AABAAsSNBCkJfFEIwwIgHRiywEZJWQZeZ2ao9AAD+4lyruzq+ngAA8Nr1AOHsBegrsOrIAAAAAElFTkSuQmCC)  
**📚 Table of Contents**  
1. Project 1 -- Install Java 21 & Jenkins  
2. Project 1 -- Uninstall Java 21 & Jenkins  
3. Project 2 -- Deploy Docker Compose Application  
4. Project 2 -- Remove Docker Compose Application  
5. Execution Commands  
6. Verification  
7. Interview Questions  
![](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAnEAAAACCAYAAAA3pIp+AAAABmJLR0QA/wD/AP+gvaeTAAAACXBIWXMAAA7EAAAOxAGVKw4bAAAANElEQVR4nO3OQQmAABRAsad4EEtY9QcxnUms4E2ELcGWmTmrKwAA/uLeqrU6vp4AAPDa/gDzXgM37EF77AAAAABJRU5ErkJggg==)  
**🏗️ Project 1 -- Install Java 21 & Jenkins**  
**Objective**  
Automatically install Java 21 and Jenkins on one or more Ubuntu servers.  
**Architecture**  
           +----------------------+  
            |   Ansible Master     |  
            +----------+-----------+  
                       |  
           SSH (Ansible Automation)  
           /                       \  
 +------------------+      +------------------+  
 |    Worker-1      |      |    Worker-2      |  
 | Java 21          |      | Java 21          |  
 | Jenkins          |      | Jenkins          |  
 +------------------+      +------------------+  
   
**Playbook**  
# jenkins.yml  
 ---  
 - name: Install Java 21 and Jenkins  
   hosts: all  
   become: true  
   
   tasks:  
     - name: Update apt cache  
       ansible.builtin.apt:  
         update_cache: true  
   
     - name: Install Java  
       ansible.builtin.apt:  
         name:  
           - fontconfig  
           - openjdk-21-jre  
         state: present  
   
     - name: Create keyring directory  
       ansible.builtin.file:  
         path: /etc/apt/keyrings  
         state: directory  
         mode: "0755"  
   
     - name: Download Jenkins key  
       ansible.builtin.get_url:  
         url: https://pkg.jenkins.io/debian-stable/jenkins.io-2026.key  
         dest: /etc/apt/keyrings/jenkins-keyring.asc  
   
     - name: Add Jenkins repository  
       ansible.builtin.copy:  
         dest: /etc/apt/sources.list.d/jenkins.list  
         content: |  
           deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/  
   
     - name: Update apt  
       ansible.builtin.apt:  
         update_cache: true  
   
     - name: Install Jenkins  
       ansible.builtin.apt:  
         name: jenkins  
         state: present  
   
     - name: Enable Jenkins  
       ansible.builtin.systemd:  
         name: jenkins  
         state: started  
         enabled: true  
   
![](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAnEAAAACCAYAAAA3pIp+AAAABmJLR0QA/wD/AP+gvaeTAAAACXBIWXMAAA7EAAAOxAGVKw4bAAAANklEQVR4nO3OQQmAABRAsSeYxZw/lVeDGMACBrCCNxG2BFtmZquOAAD4i3Ot7mr/egIAwGvXA6fOBdd+dKAKAAAAAElFTkSuQmCC)  
**🗑️ Project 1 -- Uninstall Java & Jenkins**  
---  
 - name: Uninstall Java 21 and Jenkins  
   hosts: all  
   become: true  
   
   tasks:  
     - name: Update apt cache  
       ansible.builtin.apt:  
         update_cache: true  
   
     - name: Remove Java  
       ansible.builtin.apt:  
         name:  
           - fontconfig  
           - openjdk-21-jre  
         state: absent  
   
     - name: Remove Jenkins  
       ansible.builtin.apt:  
         name: jenkins  
         state: absent  
   
![](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAnEAAAACCAYAAAA3pIp+AAAABmJLR0QA/wD/AP+gvaeTAAAACXBIWXMAAA7EAAAOxAGVKw4bAAAANElEQVR4nO3OUQmAABBAsSeIWMICprwEpjSIFfwTYUuwZWaO6goAgL+412qrzq8nAAC8tj8tdQNNdXaCdAAAAABJRU5ErkJggg==)  
**🐳 Project 2 -- Deploy Docker Compose Application**  
**docker-compose.yml**  
services:  
   web:  
     image: nginx:latest  
     container_name: nginx-web  
     restart: always  
     ports:  
       - "8090:80"  
   
**Deployment Playbook**  
---  
 - name: Deploy Docker Compose Application  
   hosts: all  
   become: true  
   
   tasks:  
     - name: Install Docker  
       ansible.builtin.apt:  
         update_cache: true  
         name:  
           - docker.io  
           - docker-compose-v2  
         state: present  
   
     - name: Enable Docker  
       ansible.builtin.systemd:  
         name: docker  
         state: started  
         enabled: true  
   
     - name: Create application directory  
       ansible.builtin.file:  
         path: /opt/nginx-app  
         state: directory  
   
     - name: Copy compose file  
       ansible.builtin.copy:  
         src: docker-compose.yml  
         dest: /opt/nginx-app/docker-compose.yml  
   
     - name: Deploy container  
       ansible.builtin.command:  
         cmd: docker compose up -d  
         chdir: /opt/nginx-app  
   
![](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAnEAAAACCAYAAAA3pIp+AAAABmJLR0QA/wD/AP+gvaeTAAAACXBIWXMAAA7EAAAOxAGVKw4bAAAANklEQVR4nO3OMQ2AABAAsSNhRAF6EPYDLhGADSywEZJWQZeZ2aszAAD+4l6rrTq+ngAA8Nr1AIWsBDYDm5cLAAAAAElFTkSuQmCC)  
**🧹 Project 2 -- Cleanup**  
---  
 - name: Delete Docker Compose App  
   hosts: all  
   become: true  
   
   tasks:  
     - name: Stop containers  
       ansible.builtin.command:  
         cmd: docker compose down  
         chdir: /opt/nginx-app  
       ignore_errors: true  
   
     - name: Remove application directory  
       ansible.builtin.file:  
         path: /opt/nginx-app  
         state: absent  
   
     - name: Remove Docker  
       ansible.builtin.apt:  
         name:  
           - docker.io  
           - docker-compose-v2  
         state: absent  
   
![](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAnEAAAACCAYAAAA3pIp+AAAABmJLR0QA/wD/AP+gvaeTAAAACXBIWXMAAA7EAAAOxAGVKw4bAAAANElEQVR4nO3OQQmAABRAsSdYxKY/jbnMIJ7FCt5E2BJsmZmt2gMA4C+Otbqr8+sJAACvXQ85TgYRMv3/cwAAAABJRU5ErkJggg==)  
**▶️ Execution**  
ansible-playbook -i inventroy.ini jenkins.yml --ask-become-pass  
   
 ansible-playbook -i inventroy.ini uninstall-jenkins.yml --ask-become-pass  
   
 ansible-playbook -i inventroy.ini deploy-docker-compose.yml --ask-become-pass  
   
 ansible-playbook -i inventroy.ini delete-docker-compose.yml --ask-become-pass  
   
![](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAnEAAAACCAYAAAA3pIp+AAAABmJLR0QA/wD/AP+gvaeTAAAACXBIWXMAAA7EAAAOxAGVKw4bAAAANElEQVR4nO3OUQmAABBAsSeIWMICprwEpjSIFfwTYUuwZWaO6goAgL+412qrzq8nAAC8tj8tdQNNdXaCdAAAAABJRU5ErkJggg==)  
**✅ Verification**  
**Jenkins**  
java -version  
 systemctl status jenkins  
   
Open:  
http://<SERVER-IP>:8090  
**Docker**  
docker ps  
 docker compose ls  
 curl http://localhost:8090  
   
![](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAnEAAAACCAYAAAA3pIp+AAAABmJLR0QA/wD/AP+gvaeTAAAACXBIWXMAAA7EAAAOxAGVKw4bAAAANklEQVR4nO3OQQmAABRAsSeYxZw/lieLGMACBrCCNxG2BFtmZquOAAD4i3Ot7mr/egIAwGvXA6fGBdgoVMwYAAAAAElFTkSuQmCC)  
**💡 Skills Demonstrated**  
- Ansible Inventory  
- YAML Playbooks  
- Become Privilege Escalation  
- APT Package Management  
- Docker Automation  
- Docker Compose  
- Service Management  
- Configuration Management  
- Infrastructure as Code (IaC)  
![](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAnEAAAACCAYAAAA3pIp+AAAABmJLR0QA/wD/AP+gvaeTAAAACXBIWXMAAA7EAAAOxAGVKw4bAAAANElEQVR4nO3OQQmAABRAsSdYxKa/i8WMIR7ECt5E2BJsmZmt2gMA4C+Otbqr8+sJAACvXQ85PAYartXEogAAAABJRU5ErkJggg==)  
**🎯 Interview Questions**  
1. Why Ansible instead of shell scripts?  
2. What is idempotency?  
3. Difference between command and shell modules?  
4. Why use become: true?  
5. How do you make playbooks reusable?  
6. How do you manage secrets in Ansible?  
7. How do you deploy Docker Compose with Ansible?  
8. What are Ansible Roles?  
9. How do you troubleshoot failed playbooks?  
10. How would you deploy this in production?  
![](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAnEAAAACCAYAAAA3pIp+AAAABmJLR0QA/wD/AP+gvaeTAAAACXBIWXMAAA7EAAAOxAGVKw4bAAAANUlEQVR4nO3OMQ2AABAAsSNhwgJWEPcbJpnRgQU2QtIq6DIze3UGAMBf3Gu1VcfXEwAAXrseaIkEMIPgIvAAAAAASUVORK5CYII=)  
**Author:** Vignesh Velmurugan  
   
 **Purpose:** DevOps Practice • Portfolio • Interview Preparation  
