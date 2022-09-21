Project Name:-Aatmaani-Project

Abstract:
Documentation and information systems are needed in all phases of the development of a software product: from the user requirements phase, through design and implementation, until delivery and maintenance of the product. For this reason documentation should be designed, managed,produced, published and maintained.
                         
Introduction: During the development of a software project, documentation is undoubtedly the activity that we all like to do the least. 
On the other hand, often programs are simply thrown away and rewritten because they are no longer under control and there is not enough information to understand them.
 This summary is an attempt to provide recipes that reduce the pain of producing that minimal amount of documentation that will help other people to work on the same project (internal documentation) and that will allow somebody outside the project to use the product (user documentation)

Tools Used:
1.GitHub:It is a place to store our peoject code and all other dependencies required for our project.
2.Jenkins(CI&CD):It is a open-source ci/cd tool widely used to integrate with different tools.
3.Docker:It is used to build docker images from our project code 
4.Kubernetes(EKS):AWS provide EKS service to deploy our images      as pods
5.Terraform:It is a infrastructure provisioning tool using terraform we can create our own custome resources in aws .
6.Prometheus & Grafana:This are monotering tools used to monitor our deployed application for better understand to maintain quality product. 

Cloud Provider and Services Used:
1.AWS cloud
2.ECR
3.EKS
4.CLOUDFORMATION
5.IAM & VPC

Project Architecture:

 

Phase-1:
Developer provided project code .
I have created a repository called Aatmaani-project in github .
In that Aatmaani-project repository i have created 2 sub repos ,(1.devops,2.nodejs-project)
Under devops repo,i included helm chart .
Helm chart consists of 3 manifest files i.e 1. dev.yaml(1 pod) ,2.qa.yaml(1 pod), 3.prod.yaml(2 pods)
If we want to access our application from outside cluster ,we need to change in every manifest file (dev.yaml,qa.yaml,prod.yaml) service to NodePort and mention Port 3000. 
Helm chart also consists templates in that we have deployment.yaml in this deployment file ,we need to change containerport to 3000,because our project is running over 3000 port.
I have wrote  a Dockerfile based on the project code and requirements.
Nodejs-project repo consists of project code and Dockerfile to create docker image
Phase 2:
Using Terraform I Created a new vpc, subnets, Kubernetes cluster with one nodegroup with min 1 spot machine, max 5 in AWS Cloud
I Created 3 different namespaces    
          1. dev    
          2. qa
          3.prod

Phase 3:
I launched one ubuntu instance (t2.medium).
Installed java 11
I installed and set up docker to build docker image.
Using ansible i installed and configured Jenkins 
After installation i configured  jobs in jenkins using pipeline
In the pipeline i used declarative pipeline concept to automate my project process
Installed required plugins like(docker pipeline,kubernetes,slack notification plugins)
Stage 1: In the pipeline i configured github project url and username with password so in stage 1 it will fetch the code from github.
Stage 2:After stage 1 ,It will get Dockerfile in this stage to build docker image and it will pushed to ECR repo (dev:latest)
Stage 3:Helm chart will pull the Image dev:latest from ECR repo (dev.yaml with one replica),it will be deployed to dev namespace using helm chart in kubernetes node .
Stage 4:If deployment failed ,slack notifications will be configured to slack channel 
Phase 4:               
Pull the image from ECR (dev:latest) change tag to qa:latest and again push to ECR 
ECR consists qa:latest image ,Helm chart in the devops repo consists of qa.yaml manifest file ,it will pull the qa:latest image 
After pulling the qa:latest image,it will deploy to qa namespace with one replica.
After that we can check the pod is running healthy and verify it will be accessible from outside world.
If deployment failed ,slack notifications will be sent slack channel .so that we will be notified .
Phase 5:
Pull the image from ECR (qa:latest) change tag to prod:latest and again push to ECR 
ECR consists prod:latest image ,Helm chart in the devops repo consists of prod.yaml manifest file ,it will pull the prod:latest image 
After pulling the prod:latest image,it will deploy to prod namespace with two replica,because we changed replica count to 2 in manifest file i.e prod.yaml
After that we can check the pod is running healthy and verify it will be accessible from outside world.
We are enabled HPA in Prod evvironment (dev.yaml file) (autoscaling)
When ever cpu stress increased to 70% ,then automatically extra pods will be created (that pods min and max given in prod.yaml file)
If deployment failed ,slack notifications will be sent slack channel .so that we will be notified.   
Phase 6:
In kubernetes cluster ,i have installed prometheus and grafana for cluster monitoring(pods,cpu utilization,Ram,memory)
Nodeport will collect all matrices from kubernetes cluster it will given to prometheus ,prometheus will display all target port matrices.
Prometheus will display all the target port matrices(default port:9090)
Grafana dashboard will run on 3000(https://local-public-ip:3000)
Conclusion:
It was a wonderful learning experience for me while working on this project. 
This project took me through the various phases of project development and gave me real insight into the world of software engineering.
