# Load-Balancing-with-Nginx-and-Docker-Containers-in-VM

Load Balancing with Nginx and Docker Containers in VM 

WorkFlow


Inside VM:





VM Setup

VM is setup using vagrant.
A virtual machine is configured to run two Docker containers.
Containers are running web applications on:
Port 8081 (Container 1)
Port 8082 (Container 2)


Docker Container Verification

Command use: docker ps 


Nginx Load Balancing Configuration (Round-Robin Technique)
Inside the VM:
Nginx is installed and configured as a reverse proxy. A round-robin load balancing technique is implemented, and requests to Nginx are distributed between the two containers.
 











Host Machine Configuration
Nginx is also installed on the host machine.Configured to pass incoming requests to the VM’s Nginx (proxy chaining).Ensures that the containers running inside the VM are accessible from the host machine.

   


Results:

Curl from Host Machine to VM’s IP, VM provide response to host machine as two containers gives.


Commond use: 
for i in {1..100}; do curl "http://192.168.32.130/" && echo  ;done


