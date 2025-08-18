# Example Voting App

A simple distributed application running across multiple Docker containers.

## Getting started

Download [Docker Desktop](https://www.docker.com/products/docker-desktop) for Mac or Windows. [Docker Compose](https://docs.docker.com/compose) will be automatically installed. On Linux, make sure you have the latest version of [Compose](https://docs.docker.com/compose/install/).

This solution uses Python, Node.js, .NET, with Redis for messaging and Postgres for storage.

Run in this directory to build and run the app:

```shell
docker compose up
```

The `vote` app will be running at [http://localhost:8080](http://localhost:8080), and the `results` will be at [http://localhost:8081](http://localhost:8081).

Alternately, if you want to run it on a [Docker Swarm](https://docs.docker.com/engine/swarm/), first make sure you have a swarm. If you don't, run:

```shell
docker swarm init
```

Once you have your swarm, in this directory run:

```shell
docker stack deploy --compose-file docker-stack.yml vote
```

## Run the app in Kubernetes

The folder k8s-specifications contains the YAML specifications of the Voting App's services.

Run the following command to create the deployments and services. Note it will create these resources in your current namespace (`default` if you haven't changed it.)

```shell
kubectl create -f k8s-specifications/
```

The `vote` web app is then available on port 31000 on each host of the cluster, the `result` web app is available on port 31001.

To remove them, run:

```shell
kubectl delete -f k8s-specifications/
```

## Architecture

![Architecture diagram](architecture.excalidraw.png)

* A front-end web app in [Python](/vote) which lets you vote between two options
* A [Redis](https://hub.docker.com/_/redis/) which collects new votes
* A [.NET](/worker/) worker which consumes votes and stores them in…
* A [Postgres](https://hub.docker.com/_/postgres/) database backed by a Docker volume
* A [Node.js](/result) web app which shows the results of the voting in real time

## Notes

The voting application only accepts one vote per client browser. It does not register additional votes if a vote has already been submitted from a client.

This isn't an example of a properly architected perfectly designed distributed app... it's just a simple
example of the various types of pieces and languages you might see (queues, persistent data, etc), and how to
deal with them in Docker at a basic level.


                  Azure Devops complete CI CD process of Vote-App Using AKS with the Commands and explanation.  


*   For creating the agent.

  - Here we use a azure virtual machine as a agent.

  - For that we should create a virtual machine and after creating we should integrate the pipelines with the agent.


* Follow the steps to connect agent.

 - Choose Azure DevOps, Organization settings.

 - Choose Agent pools.

 - Select the pool on the right side of the page and then go to agents in that pool.

 - Now go to the terminal and connect to the virtual machine.

 - When we click on the create agent on the pool of the azure devops we get all instructions and commands to create the agent.

   1)mkdir agent ; cd agent                              (Creates a folder  to hold the agent files. You move into that folder to perform the next steps.)

   2)wget <copy the command in the Download>             (This package contains the tools needed to connect your machine to Azure DevOps.)

   3)sudo apt update                                     (updating the application)


   4)Add-Type -AssemblyName System.IO.Compression.FileSystem ; [System.IO.Compression.ZipFile]::ExtractToDirectory("vsts-agent-win-x64-4.255.0.zip", "$PWD")
      
                                                         (This command extracts the downloaded ZIP file contents to the current directory. $PWD refers to the present working directory).


   5).\config.cmd   - for windows                         (This command starts the configuration wizard.).
     .\config.sh    - for linux/mac

    - Then You’ll be prompted to:

          Enter your Azure DevOps organization URL :  https://dev.azure.com/{your-organization}

          Provide a Personal Access Token (PAT) for authentication.             (You will find the PAT in the user settings of the azure devops)

          Select the agent pool.   :  Enter agent pool name.

          - Now agent will be sucessfully added 


   6) ./run.sh                                                   (Running the agent.)
                                                                - Runs the agent in the foreground (console).

                                                                - This is optional and mainly useful for testing or temporary setups.

                                                                - If you chose to install as a service during setup, this step is not required.

 




* Now connecting to the AKS cluster.

1) az login                                                                                                   (we want to login to the azure portal.)


2) az aks get-credentials --name voteappcluster --overwrite-existing --resource-group vote-app-ci             (Downloads AKS credentials and updates your local kubeconfig to talk to your cluster.


3) kubectl create namespace argocd                                     (Creates a new Kubernetes namespace called argocd. ArgoCD will be deployed and run inside this namespace.)




4) kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml                       (Installs ArgoCD components (UI, API server, controller, etc.) into the argocd namespace. The YAML is fetched directly from ArgoCD’s GitHub repo.)


5) kubectl get pods -n argocd                                      (Lists all pods in the argocd namespace to verify the deployment status.)



*  Now we have to configure argocd and connect the argocd with the azure git repo.

- so we have to get the argocd login password


6)  kubectl get secrets -n argocd                                  (Lists secrets; one of them is argocd-initial-admin-secret which contains the admin password for ArgoCD UI.)


7)  kubectl edit secret argocd-initial-admin-secret -n argocd      (Opens the secret in your default editor to view the base64-encoded admin password.)


8)  echo UDhCV0hZYjJTWWdKbDRpQQ== | base64 --decode                (Decodes the base64-encoded password to plain text. You use this password to log into the ArgoCD web UI.)



9)  kubectl get svc -n argocd                                      (Lists all services running in the argocd namespace.)



10)  kubectl edit svc argocd-server -n argocd                      (argocd-server is the main UI/API service for ArgoCD so change that to nodeport. This opens the service manifest so you can change the service type from ClusterIP (default, internal only) to NodePort (accessible externally via node IP and port).







11)  kubectl get nodes -o wide                                        (Shows the IP addresses of your cluster nodes.)



 -  Now you can access ArgoCD via the NodePort, e.g. http://<node-ip>:<node-port>. Don't forget to open the port in the VMSS of the AKS.
