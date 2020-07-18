#### Ansible connect remote host
In previous lab we have seen the deployment of tomcat.

This lab provides exercise on how to connect remote host/virtual server using inventory

#### Small introduction about inventory:

Ansible to perform task - setting up and maintaining remote servers, with a minimalist design
intended to get users up and running quick uses a file called inventory which consist of details 
related to remote infrastructure.

Lab-2 consists of the inventory file `hosts` which has details of 
```
remotehost ansible_host=<private ip/dns> ansible_connection=ssh ansible_user=<user of server example: ec2-user> 

```

`remotehost` is hosts that we are giving our remote host (VM that we want to connect remotely using SSH) as alias name to pick

`ansible_host` - details of private IP or DNS of remote server. Replace <private ip/dns> it with private ip address of remote host

`ansible_connection` - SSH mode connection from ansible server to remote host

`ansible_user` - user name which has permissions to perform SSH connection to remote host. Replace <user of server example: ec2-user> 
				with user like `ec2-user` which is EC2 VM user to perform SSH connection using private_key file.


Once we have the details of remote host in inventory file now. We have to set our provision.yml to connect remote hosts.

In `proviosing.yml` file

we are changing `hosts` from `localhost` and `connection: local`

```
---
  - hosts: localhost
    connection: local
```

to `remotehost` which is my server alias set in inventory file as below

```
---
  - hosts: remotehost
```

Now we have to copy remote server's private key to ansible server and add to ssh as below to avoid passing `--private-key` during run

``` 
$ ssh-agent bash
$ ssh-add ./<private_key.pem>
```

once keys are added, lets test the connectivity of SSH to remote host using ansible `ping` module before running playbook

```
ansible all -i hosts -m ping
```
has to return reponse as `pong` as below

```
remotehost | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false,
    "ping": "pong"
}
```

Lets run the playbook to connect remote server and deploy `tomcat`

```
ansible-playbook -i hosts site.yml -vvvv
```

-i defines inventory `hosts` above command executes tasks modules defined in playbook and run 

Alternative to pass SSH private keys if it is not added in SSH config using `--private-key`

```
ansible-playbook -i hosts site.yml --private-key ./test.pem-vvvv
```

After successful execution we can check public ip/dns of remote host at port 8080 in a browser for tomcat server response

```<public_ip>:8080```

## Useful resources: 
`Inventory:` 
https://docs.ansible.com/ansible/latest/user_guide/intro_inventory.html

`Dealing with your first inventory:`
https://docs.ansible.com/ansible/latest/network/getting_started/first_inventory.html

