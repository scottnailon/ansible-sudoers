# ansible-sudoers
Ansible playbook to ensure your servers allow the user to sudo without password

TechnoTim suggested people work out how to do this.
Make sure all your managed servers have 
- the same user / password (we will refer to your username <USERNAME> which you will need to change to the username you are using.

First, on your ansible control server/machine:
 - Create a file in /etc/sudoers.d/ called 90-user-sudoers
 - Add the following line:
 	- <USERNAME> ALL=(ALL) NOPASSWD:ALL

Your Playbook:
- Create sudoers.yml

  - hosts: "*"
    remote_user: <USERNAME> 
    become: yes
    become_method: sudo
    connection: ssh
    gather_facts: yes
    tasks:
      - name: copy sudoers
        copy:
         src: "{{ item.src }}"
         dest: "{{ item.dest }}"
         mode: "{{ item.mode }}"
        with_items:
         - { src: '/etc/sudoers.d/90-user-sudoers' ,dest: '/etc/sudoers.d/90-user-sudoers' ,mode: '0755'}

Execute it:

	ansible-playbook playbooks/sudoers.yml --user <USERNAME> --ask-pass --ask-become-pass -i inventory/hosts
