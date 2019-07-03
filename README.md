# Building the project with Kaniko

We are taking the base of my [previous example project](https://github.com/WorkshopCB/spring-petclinic) using Docker container to build Docker (DoD). But in this repo we are going to replace the Docker build phase to use [Kaniko](https://github.com/GoogleContainerTools/kaniko#kaniko---build-images-in-kubernetes), a safest way to build Docker containers in Kubernetes.

The pipeline to execute in CloudBees Core (Jenkins also) is:
- Maven stage with a Maven container
- Docker build and Docker push stages using Kaniko
- Deploy stage into K8s using a container with kubectl tool

Tu use Kaniko we need to do some configuration in oder to build the pipeline in Jenkins/CBCore

## Creating a docker registry secret with kubectl tool

We are creating a K8s secret for the docker registry credentials as follow:
```
$ kubectl create secret docker-registry docker-credentials \
    --docker-username=<username>  \
    --docker-password=<password> \
    --docker-email=<email-address>
```

### If you are using GCR credentials

In the case that you use Google Cloud Platform for your container registry (Google Container Registry or GCR) and needs credentials from the Cloud Platform, you will need to create a [JSON Key file for your Service Account](https://support.google.com/cloud/answer/6158849#serviceaccounts). Then the previous command to create your Kubernetes secret would be like:
```
$ kubectl create secret docker-registry my-docker-gcr \
 --docker-username=_json_key \ 
 --docker-password="$(cat <your_keyjson_file.json>)" \
 --docker-email=<your_email_address> \

```

Note that the username to use in this case is `_json_key`as the [documentation in GCP](https://cloud.google.com/container-registry/docs/advanced-authentication) requires.

## Deploying Docker secret with a YAML file

Instead of creating the Docker secrets with kubectl you can create the secret deploying a file similar to [secret_template.yaml file example](./secret_template.yaml) in this repo. Just change your credentials in the file and deploy it:
```
kubectl apply -f secret_template.yaml
```
NOTE: Please, change your parameters in `secret_template.yaml`

## Using Kanino container in your pipeline

We need to configure a Pod Template where Kaniko container is used to build and push our Docker images and then add that container into our stage in the Jenkins pipeline:
- [Pod Template](./podtemplate.yaml)
- [Jenkinsfile](.Jenkinsfile)

## Testing that Kaniko works

This repo includes a yaml file to test that Kaniko can push images to your Docker registry. You can do that by executing:
```bash
kubectl apply -f generic_example_kaniko.yaml
```
NOTE: Please, change the `--destination` argument for your Docker Resgistry used.







