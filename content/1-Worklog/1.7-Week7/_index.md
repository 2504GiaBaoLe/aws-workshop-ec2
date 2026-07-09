---
title: "Week 7 Worklog"
date: 2024-01-01
weight: 7
chapter: false
pre: " <b> 1.7. </b> "
---


### Week 7 Objectives:

* Understand the fundamentals of Containers and Container Orchestration.
* Learn the basics of Docker and its core components.
* Explore Amazon Elastic Container Registry (Amazon ECR).
* Learn how Amazon Elastic Container Service (Amazon ECS) manages containerized applications.
* Build and deploy a simple containerized web application on AWS.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | ---- | ---------- | --------------- | ------------------ |
| 2 | - Learn the fundamentals of Containers and Docker <br> - Understand Docker Images, Containers, and Dockerfiles <br> - Learn the Container lifecycle | 05/01/2026 | 06/01/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3 | - Install Docker <br> - Practice basic Docker commands <br>&emsp; + `docker build` <br>&emsp; + `docker run` <br>&emsp; + `docker ps` <br>&emsp; + `docker images` | 06/02/2026 | 06/02/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 4 | - Learn Amazon ECR <br> - Create an ECR Repository <br> - Push Docker Images to Amazon ECR | 06/03/2026 | 06/03/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 5 | - Learn Amazon ECS <br>&emsp; + ECS Cluster <br>&emsp; + Task Definition <br>&emsp; + ECS Service <br>&emsp; + Launch Types | 06/04/2026 | 06/04/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 6 | - **Hands-on:** <br>&emsp; + Build a containerized web application <br>&emsp; + Push the Docker Image to Amazon ECR <br>&emsp; + Deploy the application using Amazon ECS | 06/05/2026 | 06/05/2026 | <https://cloudjourney.awsstudygroup.com/> |

### Week 7 Achievements:

* Understood the concept of **Containers** and the benefits of containerizing applications using Docker.

* Learned the core components of Docker, including:
  * Docker Engine
  * Docker Image
  * Docker Container
  * Dockerfile
  * Docker Hub

* Successfully installed Docker and performed basic operations such as:
  * Building Docker Images
  * Running and managing Containers
  * Listing Images and Containers
  * Removing unused Images and Containers

* Learned how to package an application into a Docker Image using a Dockerfile.

* Explored **Amazon Elastic Container Registry (Amazon ECR)**, including:
  * Creating Private Repositories
  * Authenticating with Amazon ECR
  * Tagging Docker Images
  * Pushing Docker Images to ECR
  * Managing Image versions

* Understood the architecture of **Amazon Elastic Container Service (Amazon ECS)**, including:
  * ECS Clusters
  * Task Definitions
  * ECS Tasks
  * ECS Services
  * Launch Types (EC2 and AWS Fargate)

* Learned the workflow for deploying containerized applications on AWS:
  * Build a Docker Image
  * Push the Image to Amazon ECR
  * Create an ECS Cluster
  * Register a Task Definition
  * Deploy an ECS Service

* Successfully deployed a simple containerized web application using Amazon ECS and verified that the service was running correctly.

* Gained a clear understanding of the roles of Amazon ECR and Amazon ECS:
  * Amazon ECR stores and manages container images.
  * Amazon ECS orchestrates and manages containerized workloads.

* Acquired the ability to build a basic container deployment pipeline using Docker, Amazon ECR, and Amazon ECS.