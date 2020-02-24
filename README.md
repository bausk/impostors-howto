# Impostor's Howto

A collection of notes for a meaningful software engineering career.

## How to Use

This is a collection of notes that I try to take when researching a software engineering or computer science topic.  

The notes are usually made with internal usage in mind to alleviate my poor memory and therefore tend to be exceedingly laconic. Use at your own dicretion.

## Foundational Reading - Abridged Notes

- 2019: [Designing Data-Intensive Applications: Abridged Notes](https://gist.github.com/bausk/f45d9ca836e0a3d623d3ae2389ec5eac)

- 2017: [Clean Code (very short note)](https://gist.github.com/bausk/0b0723bd8e1193c1342e658771a002cf)

## 1. Workflow, DX, DevOps and Infrastructure

DX stands for "Developer Experience", that is, getting a development workflow that is stable, reproducible, and easy to use. 
My notes focus on making it work on a Windows PC while using containers, WSL, and remote Linux/PAAS machines where possible.

### Prerequisites

- [Setting up Cloud Providers for Local Development](cloud-setup.md)

### Python

All Python tutorials have the [onilapp example application](https://github.com/bausk/onilapp) as a source example. You can just start with the first onw.

- [Starting and Deploying a Python Project](python_01_start_and_deployment.md)

- [Consuming Data for Production in Python Apps](python_02_consuming_data.md)

- [Deploying to AWS Lambda with Zappa](python_03_awslambda.md) (deprecated, use `sls` below)

- [Deploying to AWS Lambda with Serverless Framework](python_04_serverless.md)

- [Static Deploys](python_05_staticdeploys.md)

### JAMStack

After I was done with building an MVP with Dash in Python, I tried to split up the app components into a JAMStack architecture. The notes documenting this start here.

- [JAMStack Intro](jamstack_01_intro.md)

### Containerization

- [How Do I... in Docker](containers_docker.md)

- [Using Kubernetes for Web Development](containers_k8s.md)


## 2. Concurrency and Execution Models

* [`asyncio` Seminar given at Webinerds in Dnipro, 2018](https://github.com/bausk/seminar2018)

## 3. Machine Learning

* TBA
