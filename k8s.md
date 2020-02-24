# k8s-web-tutorial
A compilation of tutorials to set up and use containerization for web development and DevOps.

## General Kubernetes Local Setup

### Before You Begin

This tutorial is primarily for Windows users. The workflow for other systems is the same but you may need to consult the links to official documentation. I try to point out differences in files and setups where possible.

### How this Tutorial is Organized

Before each chapter, I will give a checklist of stuff to install, test, or confirm to be working. Where options about what software to install are possible, I'll give my own preferences as commentary to the checklist

### Prerequisites

#### Checklist

Installed:
- `git`
- Node.js
- `npm`
- Python 3.x
- ConEmu or Cmder for terminal
- Sublime Text 3 for text edits
- VirtualBox VM
- working `minikube` console command

This tutorial assumes you have `git`, Node.js, `npm`, and Python installed. On Windows, you are advised to get a good console emulator and a text editor.

To start, you need to setup `kubectl` and `minikube` on your system. Follow the [k8s tutorial](https://kubernetes.io/docs/tasks/tools/install-minikube/).

Now remember, this part is opinionated and an alternative to the official tutorial linked above. If you want to shortcut through the tutorial, then, on Windows:

- Install VirtualBox VM.
- Turn off Hyper-V on your machine.
- Install Google Cloud SDK https://cloud.google.com/sdk/docs/quickstart-windows
- Run `gcloud init` and login.
- Download Minikube https://github.com/kubernetes/minikube/releases
- Start new conemu console in a new window in order to reload the update PATH variable.
- rename minikube executable to minikube.exe and move in to where `where gcloud` points you in the console.
- Run `minikube start` from the console while located on the same drive as your system.

This should set you up with a local k8s development cluster managed by minikube.

### Creating Test Deployment

Make sure you're following the k8s [bootcamp tutorial](https://kubernetes.io/docs/tutorials/kubernetes-basics/deploy-app/deploy-interactive/). With the minikube virtual machine set up, we can create a test deployment from the tutorial:

```
λ kubectl run kubernetes-bootcamp --image=gcr.io/google-samples/kubernetes-bootcamp:v1 --port=8080
λ kubectl proxy
```

Now start a duplicate console window with bash::bash preset in order to be able to do trickier commands:

```
λ export POD_NAME=$(kubectl get pods -o go-template --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}')
λ echo $POD_NAME
kubernetes-bootcamp-5c69669756-hcpcj
```

## Example: Building Docker Container for Existing Dotnet Backend

### Setup

- Run `git clone https://gitlab.webinerds.com/snapbook/snapbook.git` to get the repo.
- Read about [Docker Multistage Builds](https://docs.docker.com/develop/develop-images/multistage-build/#use-multi-stage-builds).
- Using as example:
https://hjerpbakk.com/blog/2018/06/25/aspnet-react-and-docker
https://www.peterbe.com/plog/how-to-create-react-app-with-docker
- Create dockerfile with the following content:
