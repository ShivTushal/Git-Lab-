# Flask CI/CD Pipeline with GitLab and Docker

This project demonstrates a complete CI/CD workflow using:

- Flask
- Docker
- GitLab CI/CD
- Docker Hub
- AWS EC2
- Automated Deployment

---

# Project Architecture

```text
Developer Pushes Code
        ↓
GitLab Pipeline Triggered
        ↓
Test Stage
        ↓
Build Docker Image
        ↓
Push Image to Docker Hub
        ↓
Deploy to AWS EC2
        ↓
Run Flask Container
```

---

# Technologies Used

- Python 3.11
- Flask
- Docker
- GitLab CI/CD
- AWS EC2
- Docker Hub
- Linux
- YAML Pipelines

---

# CI/CD Pipeline Stages

## 1. Test Stage

The pipeline validates the Flask application before deployment.

### Tasks Performed
- Install dependencies
- Validate Python syntax
- Run automated checks

---

## 2. Build Stage

The pipeline:
- Builds Docker image
- Tags the image
- Pushes image to Docker Hub

### Docker Image

```text
shivtushal/git-lab:python-app-1.0
```

---

## 3. Deploy Stage

The pipeline:
- Connects to AWS EC2 using SSH
- Stops old containers
- Pulls latest Docker image
- Runs updated Flask application

---

# GitLab CI/CD Pipeline

```yaml
variables:
  IMAGE_NAME: shivtushal/git-lab
  IMAGE_TAG: python-app-1.0

stages:
  - test
  - build
  - deploy

run_tests:
  stage: test

  image: python:3.11

  before_script:
    - pip install flask pytest

  script:
    - python -m py_compile app.py
    - echo "Flask application syntax validation successful"

build_image:
  stage: build

  image: docker:20.10.16

  services:
    - docker:20.10.16-dind

  variables:
    DOCKER_TLS_CERTDIR: "/certs"

  before_script:
    - docker login -u $REGISTRY_USER -p $REGISTRY_PASS

  script:
    - docker build -t $IMAGE_NAME:$IMAGE_TAG .
    - docker push $IMAGE_NAME:$IMAGE_TAG

  needs:
    - run_tests

deploy:
  stage: deploy

  image: alpine:latest

  before_script:
    - apk add --no-cache openssh-client
    - chmod 400 $SSH_KEY

  script:
    - ssh -o StrictHostKeyChecking=no -i $SSH_KEY ubuntu@13.232.167.146 "
        sudo docker login -u $REGISTRY_USER -p $REGISTRY_PASS &&
        sudo docker ps -aq | xargs -r sudo docker stop | xargs -r sudo docker rm &&
        sudo docker run -d -p 5000:5000 $IMAGE_NAME:$IMAGE_TAG
      "

  needs:
    - build_image
```

---

# Dockerfile

```dockerfile
FROM python:3.11

WORKDIR /app

COPY . .

RUN pip install flask

EXPOSE 5000

CMD ["python3", "app.py"]
```

---

# AWS EC2 Deployment

The Flask application is deployed automatically to an AWS EC2 instance using SSH-based deployment automation.

### EC2 Features
- Dockerized deployment
- Automated container replacement
- Continuous deployment through GitLab CI/CD

---

# Docker Commands Used

## Build Docker Image

```bash
docker build -t shivtushal/git-lab:python-app-1.0 .
```

## Push Docker Image

```bash
docker push shivtushal/git-lab:python-app-1.0
```

## Run Container

```bash
docker run -d -p 5000:5000 shivtushal/git-lab:python-app-1.0
```

---

# Features

- Automated CI/CD pipeline
- Docker containerization
- Automated deployment to AWS EC2
- Multi-stage GitLab pipeline
- Real-time deployment workflow
- Flask web application hosting

---

# Learning Outcomes

Through this project, I learned:

- GitLab CI/CD pipelines
- Docker containerization
- YAML pipeline configuration
- Automated deployments
- AWS EC2 deployment automation
- SSH-based remote deployments
- Docker Hub integration
- DevOps workflow automation

---

# Future Improvements

- Add unit testing with Pytest
- Add Kubernetes deployment
- Add NGINX reverse proxy
- Add HTTPS using SSL
- Add monitoring and logging
- Deploy using Kubernetes or ECS

---

# Author

Shiv Tushal
