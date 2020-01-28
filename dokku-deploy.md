# Impostor's Howto #3: Setting Up Deployment

## Problem

I want to have a robust deployment process that supports environments.
I want to spend as little as possible on hosting.

## Where we pick up prior art

- WSL 1 or 2 with Docker and Docker-Compose installed ([workflow-setup.md](workflow-setup.md)).
- A Google Cloud or AWS virtual machine is set up.
- Dokku is installed.

## Solutions

### Setting up tooling

Install `gcloud` as an example of virtual machine provisioning CLI. In commnad line, set up `gcloud`:

``` bash
gcloud config set account <account>@gmail.com
gcloud auth login
gcloud compute ssh <dokku-host> # Remote terminal into dokku host VM
