# Containerization-of-a-microservice-application
This project contains the documentation of the implementation details for the containerizing an existing micro services application using Docker and publishing the images on Dockerhub.

## Overview
The project aims to address the **challenges** faced by operations or DevOps teams managing **multi-tier application stacks** running on various platforms, including VMs, cloud environments like AWS, or physical machines in a data center. These challenges include **high costs, resource wastage, human errors** during deployment, and **lack of synchronization across different environments**. The proposed solution is to **containerize** the application, which can **save money** by using fewer resources, make deployments more **reliable** through the use of container images, and improve **portability** and **reusability** across environments. This approach is particularly suitable for **microservice architecture**. By containerizing the application, the team can achieve **significant benefits** and improve their operations.

## Services Used
- AWS EC2
- Dockerhub
- Docker Engine
- Github

## Implementation Details

### Create Repositories on Dockerhub
**On Dockerhub**
- Go to *Repositories->Create repository*-> Give a name and create.
- In this way create 3 public repositories to host app, db and web images.

### Configure EC2 instance and Docker engine
- Launch an EC2 instance with the following specifications:
    - Instance type: t2.small/t2.medium (higher RAM is required for proper functioning of docker engine)
    - AMI: Ubuntu 22
    - Security Group inbound rules : 
        - port 22 allowed from your IP
        - port 80 allowed from your IP.
    - User data content:
    ```bash
    #!/bin/bash
    sudo apt-get update
    sudo apt-get install ca-certificates curl gnupg -y
    sudo install -m 0755 -d /etc/apt/keyrings
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg -y
    sudo chmod a+r /etc/apt/keyrings/docker.gpg
    echo \
    "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
    "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
    sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
    sudo apt-get update
    sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y
    ```
    - This will install docker engine on your instance while it is being launched.

### Fork vprofile-project repository into your account
- **On Github**
- Fork the vprofile-project repository with the same name into your account.
- Switch to branch containers which contains empty docker files which you can edit.

### Write dockerfiles to customize images
- Clone this repository into your machine.
- Switch to branch containers using the command
```bash 
git checkout containers
```
- Then edit the following:
    - Insert contents into Docker-files/db/Dockerfile from the https://github.com/SumitM01/vprofile-project/blob/containers/Docker-files/db/Dockerfile file.
    - Insert contents into Docker-files/web/Dockerfile from the https://github.com/SumitM01/vprofile-project/blob/containers/Docker-files/web/Dockerfile file.
    - Insert contents into Docker-files/app/Dockerfile from the https://github.com/SumitM01/vprofile-project/blob/containers/Docker-files/app/Dockerfile file.
    - Insert the contents into Docker-compose.yaml file from https://github.com/SumitM01/vprofile-project/blob/containers/docker-compose.yml file.
- Commit and push the changes into the remote repository.

### Run the files and upload images to Dockerhub
- ssh to the ec2 instance.
- Clone the remote repository from github.
- Switch to containers branch.
- Validate the changes made.
- Run the following command to build the images:
```bash
docker compose build
```
- Run the following command to see the built images:
```bash
docker images
```
- Run the following command to spin up all the containers from the images in the background:
```bash
docker compose up -d
```
- Run the following command to login to Dockerhub
```bash
docker login
```
    - Provide username and password to login.
- Run the following command to upload images to dockerhub:
```bash
docker push your_account_name/repository_name
```
- Logout from docker hub using 
```bash
docker logout
```
### Stop the containers and clean up
- After validation and uploading is completed perform the following commands to clean up:
```bash
docker compose down
docker system prune -y
```

## Results
As the containers are deployed, I validated them by visiting the following pages:
- **Login Page**
![login-page](https://github.com/SumitM01/CI-CD-project-using-AWS-CodeCommit/assets/65524561/7efbbbe6-d450-4780-a54a-9ed6fec2e4be)
- **Home Page**
![home-page](https://github.com/SumitM01/CI-CD-project-using-AWS-CodeCommit/assets/65524561/b3dade74-61d4-4867-9938-46eedf123423)

Here we can see that the backend services RDS, Elasticache and RabbitMQ are also configured correctly.
- **Rabbitmq status**
![rabbitmq-start-success](https://github.com/SumitM01/CI-CD-project-using-AWS-CodeCommit/assets/65524561/c600d583-18e2-4daf-845c-39febf678ab3)
- **User Details Page**
![user-details-page](https://github.com/SumitM01/CI-CD-project-using-AWS-CodeCommit/assets/65524561/0e7aa500-b972-4ff6-ba4a-f687c115f826)

## Conclusion
To conclude, the project provides a cost efficient way to manage infrastructure by using containers to host applications. I have successfully conatinerized the existing application using docker and uploaded the images
to dockerhub for your viewing.
As documented in this markdown format file, I have invested a significant amount of time in researching, learning, debugging to implement this project. If you appreciate this document please give it a ⭐, share with friends and do give it a try. 
## References
- https://www.udemy.com/course/decodingdevops
- https://github.com/devopshydclub/vprofile-project/tree/docker
- https://docs.docker.com
- https://hub.docker.com