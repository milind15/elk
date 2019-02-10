CONTAINERIZED DEPLOYMENT OF ELK STACK

What is ELK?

"ELK" is the acronym for three open source projects: ELASTICSEARCH, LOGSTASH and KIBANA. Elasticsearch is a search and analytics engine. Logstash is a server-side data processing pipeline that ingests data from multiple sources simultaneously, transforms it, and then sends it to a "stash" like Elasticsearch. Kibana lets users visualize data with charts and graphs in Elasticsearch.

a. Tools & Technology Used:

        1) Freshly deployed ubuntu 18.04 server with 2GB Memory
	2) Docker CE
	3) Docker Compose

b.1 Assumptions:
	
         1) Root access or sudo to root access on server which is being used for deployment
	 2) Access to internet on the server

b.2 Prerequisites:
	
        1) Docker CE & Docer compose should be configured properly.
        2) git should be installed on the machine.

c. Instructions to set up on new linux machine:
	
       Run following commands on the vm as root user:
	$ apt-get update
	$ apt-get remove docker docker-engine -y
	$ apt-get install apt-transport-https ca-certificates curl gnupg2 software-properties-common python-setuptools -y
	$ curl -fssl https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
	$ add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu ${lsb_release -cs} stable"
	$ apt-get update
	$ apt-get install docker-ce -y
	$ systemctl enable docker
	$ systemctl restart docker
	$ apt install python-pip
	$ pip install docker-compose
	$ usermod -aG docker <your-user>

   Testing:
	
        $ docker --version
	$ docker-compose --version
	$ docker run hello-world (docker test container)
	Successful output of above commands verifies the environment is completely setup now.

d. Steps to run the project:
	
       Clone the git repository for getting required files.
	$ git clone https://github.com/milind15/elk.git
	$ cd elk
	$ cd elasticsearch
	$ docker build -t elasticsearch .
	$ cd ..
	$ cd logstash 
	$ docker build -t logstash .
	$ cd ..
	$ cd kibana
	$ docker build -t kibana .
	$ cd ..
	$ docker images
	You should be able to see the built images & the base images.
   
   Running the complete stack in one go:
	
        $ docker-compose up
	Within few moments, curl http://localhost:5601(blank output) or open in your browser & you should be able to see kibana running 	successfully.
   
   Verifying the elk containers are running
	
        $ docker container ls
	You can see the 3 containers elastic, kibana, logstash running successfully.

e. Testing the deployment:
	
    * Open http://localhost:5601 in your browser & access kibana using some sample data. You will be able to see the dashboard with the sample data represented by charts & graphs.
    * curl http://localhost:9200 , you will see a JSON response from elasticsearch.
    * Healthchecks can be enabled in dockerfile for elasticsearch & kibana for monitoring container health on deployment.
    * Use docker logs <container-name> to see the logs for the running containers
    * For negative testing, remove the logstash container using docker container rm elk_logstash_1, run again
    $ docker-compose up -d
    You will see the existing 2 containers will not be modified but docker-compose creates the missing container logstash.

f. Troubleshooting Guidelines:
	
    Inorder to run elasticsearch as a container.The vm.max_map_count kernel setting needs to be set to at least 262144 for production use.
    run command : sysctl -w vm.max_map_count=262144
    Make sure to allow http traffic on port 5601 & port 9200 to access kibana & Elasticsearch from your browser.

       
  ***THE ELK STACK HAS BEEN DEPLOYED SUCCESSFULLY USING DOCKER COMPOSE***

    1) This architecture can be scaled horizontally by making a 3 node swarm cluster which will provide enhanced scaling & load balancing in large data scenarios since each node will be running a single service or there will be multiple containers of every service distributed among the swarm.In case of 100 TB of data, Vertical scaling can be done in the swarm by adding more physical resources such as memory, storage and CPU to the existing database server for improving the performance.It results in a robust system. 
    2) In case of a dashboard vs an application that consumes this data for data mining the scaling process will remain same.
    3) Such cluster can be managed by making a manager node among 3 nodes available.The docker swarm manager node uses the "Raft Consensus Algorithm" to manage the swarm state.In case of a big cluster there can be multiple swarm managers managing respective worker nodes.
    4) For managing container runtime security :
     
     1) Secure the OS and reduce attack surfaces by removing all unneeded modules and files. Be diligent in updating to the latest security patches.

     2) Ensure the container platform is secured.Follow docker best practice's guide.

     3) Use solutions like SELinux or AppArmor to customize specific security profiles and guard against unauthorized access.

     4) Scan containers for vulnerabilities in all registries.

     5) Run integrity checks on container images and digitally sign them at build time.
 
     6) Protect secrets such as passwords and API keys required for run-time container access by using docker secrets.

     7) Reduce your risk by running application containers in read-only/non-persistent mode.

     Then, constantly monitor and protect the run-time environment:

     8) Understand normal application network behavior, and enact a security policy to enforce authorized connections. Monitor every  container for abnormal behavior or policy violations.

     9) Perform live scans of all running containers and hosts, recognizing vulnerabilities and securing the image in use – even as new containers are created.

     10) Use session level or network encryption where needed. Carefully weigh the tradeoffs between performance/manageability versus security for each application to determine if host-to-host or container-to-container level encryption is warranted.

     11) Implement container threat detection to recognize real-time attacks, including threats at the application layer.

     12) Store forensic data on all container security events and perform offline analysis to understand the nature of these events.Capture network data if needed to help forensic analysis of attacks. 














 
