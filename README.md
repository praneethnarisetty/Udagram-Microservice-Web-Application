# udagram-microservice
Udagram is a simple cloud application developed alongside the Udacity Cloud Engineering Nanodegree. It allows users to register and log into a web client, post photos to the feed, and process photos using an image filtering microservice.

## Tasks

### Setup Docker Environment
You'll need to install docker https://docs.docker.com/install/. Open a new terminal within the project directory and run:

1. Switch the folder: `cd udacity-c3-deployment/docker`
2. Build the images: `docker-compose -f docker-compose-build.yaml build --parallel`
3. Push the images: `docker-compose -f docker-compose-build.yaml push`
4. Run the container: `docker-compose up`



### Creating Infrastructure with terraform 

1. Go to your terraform/aws folder
2. See what commands will be made to create infrastructure: `terraform plan`
3. Provision the infrastructure: `terraform apply`
	- Say `yes` to confirm provisioning the infrastructure
4. Create Terraform state file to be parsed by kubeone: `terraform output -json > tf.json`
5. Install Kubernetes using configuration output from Terraform: `kubeone install config.yaml --tfjson tf.json`
6. Setup KUEBCONFIG variable that will be used by kubectl commands, run this command in any terminal window that the kubectl command will be run in
	- `export KUBECONFIG=$PWD/udagrambox`

### Setup Kubernetes environment
You will need to install the kubectl command. Open a new terminal within the project directory and run:

1. Generate encrypted values for aws credentials, Database User Name, and Database Password using bcrypt and put the values into aws-secret.yaml and env-secret.yaml files
2. Load secret files: 
	- `kubectl apply -f aws-secret.yaml`
	- `kubectl apply -f env-secret.yaml`
3. Load config map: `kubectl apply -f env-configmap.yaml`
4. Apply Deployments:
	- `kubectl apply -f backend-feed-deployment.yaml`
	- `kubectl apply -f frontend-deployment.yaml`
	- `kubectl apply -f backend-user-deployment.yaml`
5. Apply Services:
	- `kubectl apply -f backend-feed-service.yaml`
	- `kubectl apply -f backend-user-service.yaml`
	- `kubectl apply -f frontend-service.yaml`
6. Deploy reverse proxy
	- `kubectl apply -f reverseproxy-deployment.yaml`
	- `kubectl apply -f reverseproxy-service.yaml`
7. Perform port forwarding 
	- `kubectl port-forward service/frontend 8100:8100`
	- `kubectl port-forward service/reverseproxy 8080:8080`

### Continuous Integration / Continuous Development:
- Travis CI is setup to monitor for updates to master branch