name: gitlab-cicd-sonar-go
displayName: Gitlab CI/CD with SonarQube for Golang 
type: base

categories: 
  - Website

version: 0.0.1
license: MIT
channel: https://mm.nttdata-labs.com/emea-lab/channels/town-square

description: Template to generate a Gitlab CI/CD with SonarQube for hello world program in golang.
longDescription: |
  This template generates a Hello World application in Golang with Gitlab CI/CD pipeline definition and SonarQube integration.

  The main purpose is to demonstrate end to end process from code generation to deployment to Kubernetes. Integration of additional tools into CI/CD pipeline, like SonarQube, is also shown.
  This template requires manual steps for integration of Gitlab with SonarQube and Kubernetes. Detailed procedure is described in Usage chapter.

  In addition, this template demonstrates how parameters can be utilized to customize the code generation process. It is possible to customize the response greeting using the Altemista CLI. For example, if you want to greet yourself then generate the code with the following command:

  ```bash
  $ altemista new gitlab-cicd-sonar-go -- greeting=Myself
  ```

  To learn more about the full template specification:

  [https://github.com/Altemista/Template](https://github.com/Altemista/Template)

  ### Features
  
  * Code for a simple http server
  * Dockerfile
  * CI/CD for Gitlab
  * Code quality with SonarQube
  * Deployment to Kubernetes
  
  ### Usage
  
  ## Prerequisites

  * Gitlab with integrated docker registry
  * Gitlab runner
  * Kubernetes cluster with ingress controller and cert-manager
  * SonarQube with Go Plugin
  * Base domain configured to route traffic to Kubernetes cluser, for example CNAME record *.demo-cluster.altemista.cloud pointing to AWS LB

  ## Code Generation

  1. Generate code by pressing Generate button and following instructions
  
  ## Prepare Kubernetes Cluster
  
  1. Create namespace for application, for example hello-go: `kubectl create ns hello-go`
  2. Create service account gitlab-ci and role bindings by applying generated rbac.yaml from deployment directory:
     `kubectl apply -f deployment/rbac.yaml`
     
   ## Integrate Gitlab with Kubernetes Cluster
   
   1. Get api url
   `kubectl cluster-info | grep 'Kubernetes master' | awk '/http/ {print $NF}'``
   2. Get CA certificate
   `kubectl get secrets & kubectl get secret <secret name> -o jsonpath="{['data']['ca\.crt']}" | base64 --decode`
   3. Get service token
   `kubectl -n hello-go describe secret $(kubectl -n hello-go get secret | grep gitlab-ci-token | awk '{print $1}')`
   4. Using gathered information, configure Gitlab integration with Kubernetes, like on image below:
   
   ![Confugure_kubernetes_cluster](https://raw.githubusercontent.com/Altemista/hello-go/master/pics/hello-go.png =531x166)
   
  ## Integrate Kubernetes with Gitlab Registry
  
  1. On project level, create deployment token with read_registry permission: Settings > Repository > Deploy Tokens
  2. Create secret in existing cluster
  ```bash
  kubectl create -n hello-go \
  secret docker-registry gitlab-registry-credentials \
  --docker-server=registry.altemista.cloud \
  --docker-username=[username] \
  --docker-password=[token]
  ```
  3. Add pull secret credential to default service account in hello-go namespace
  ```bash
  kubectl patch -n hello-go \
  serviceaccount default \
  -p '{"imagePullSecrets": [{"name": "gitlab-registry-credentials"}]}'
  ```
  
   ## Integrate SonarQube with Gitlab

  1. Create user token in Sonarqube: My Account > Security > User Token
  2. Create new repository (project) in Gitlab, for example demo-gitlab-sonar-go
  3. In new project, add Gitlab environment variables for integration with SonarQube
  ```bash
  SONAR_HOST_URL = [url] - for example https://sonarqube.demo-cluster.altemista.cloud
  SONAR_TOKEN = [token_from_sonarqube]
  ```
  
  ## Link Generated Code with GItlab Repository

  1. Push generated code to existing repository
  ```bash
  cd existing_folder
  git init
  git remote add origin [repo_url]
  git add .
  git commit -m "Initial commit"
  git push -u origin master
  ```
  2. Monitor pipeline execution in Gitlab

  ## Verify Application

  1. Access application using curl
  ```bash
  $ curl [app_url] for example, curl https://hello-go.demo-cluster.altemista.cloud
  ```
  
    
repository: 'https://github.com/Altemista/gitlab-cicd-sonar-go'
provider: 'NTT Data'
createdAt: '2020-03-09T00:00:00Z'
maintainers:
  - name: 'Mile Misan'
    email: Mile Misan <mile.misan.bp@nttdata.com>

commands:
  - git clone https://github.com/Altemista/gitlab-cicd-sonar-go.git . && rm -r .git
  - sed -i 's/{{name}}/'"$greeting"'/g' main.go
  
parameters:
- name: greeting
  type: input
  message: "Who should be greeted"
  default: World
  tooltip: Parameter to replace the default 'World' in 'Hello World!', e.g. 'Hello NTT'
  url: https://github.com/Altemista/hello-go/blob/master/main.go#L11
  
icon:
- base64data: PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz4KPCEtLSBHZW5lcmF0b3I6IEFkb2JlIElsbHVzdHJhdG9yIDE4LjEuMCwgU1ZHIEV4cG9ydCBQbHVnLUluIC4gU1ZHIFZlcnNpb246IDYuMDAgQnVpbGQgMCkgIC0tPgo8c3ZnIHZlcnNpb249IjEuMSIgaWQ9IkdvcGhlciIgeG1sbnM9Imh0dHA6Ly93d3cudzMub3JnLzIwMDAvc3ZnIiB4bWxuczp4bGluaz0iaHR0cDovL3d3dy53My5vcmcvMTk5OS94bGluayIgeD0iMHB4IiB5PSIwcHgiCgkgdmlld0JveD0iMCAwIDIxNS42IDI4MS42IiBlbmFibGUtYmFja2dyb3VuZD0ibmV3IDAgMCAyMTUuNiAyODEuNiIgeG1sOnNwYWNlPSJwcmVzZXJ2ZSI+CjxnPgoJPHBhdGggZmlsbD0iIzhDQzVFNyIgZD0iTTIwNy4zLDQ0LjZjLTYuNy0xMy43LTIyLjktMS42LTI3LTUuOWMtMjEtMjEuNi00Ni40LTI3LTY2LjMtMjhjMCwwLTksMC0xMSwwYy0yMCwwLjUtNDUuNCw2LjMtNjYuMywyOAoJCWMtNC4xLDQuMy0yMC40LTcuOC0yNyw1LjljLTcuNywxNiwxNS43LDE3LjYsMTQuNSwyNC43Yy0yLjMsMTIuOC0wLjgsMzEuOCwxLDUwLjVDMjgsMTUxLjUsNC4zLDIyNy40LDUzLjYsMjU3LjkKCQljOS4zLDUuOCwzNC40LDksNTYuMiw5LjVsMCwwYzAsMCwwLjEsMCwwLjEsMGMwLDAsMC4xLDAsMC4xLDBsMCwwYzIxLjgtMC41LDQzLjktMy43LDUzLjItOS41YzQ5LjQtMzAuNSwyNS43LTEwNi40LDI4LjYtMTM4LjEKCQljMS43LTE4LjcsMy4yLTM3LjcsMS01MC41QzE5MS42LDYyLjIsMjE1LDYwLjUsMjA3LjMsNDQuNnoiLz4KCTxnPgoJCTxwYXRoIGZpbGw9IiNFMERFREMiIGQ9Ik0xNDMuMiw1NC4zYy0zMy40LDMuOS0yOC45LDM4LjctMTYsNTBjMjQsMjEsNDksMCw0Ni4yLTIxLjJDMTcwLjksNjIuNywxNTMuNiw1My4xLDE0My4yLDU0LjN6Ii8+CgkJPGNpcmNsZSBmaWxsPSIjMTExMjEyIiBjeD0iMTQ1LjUiIGN5PSI4NC4zIiByPSIxMS40Ii8+CgkJPGNpcmNsZSBmaWxsPSIjRkZGRkZGIiBjeD0iMTQyLjUiIGN5PSI3OS40IiByPSIzLjYiLz4KCTwvZz4KCTxnPgoJCTxwYXRoIGZpbGw9IiNCODkzN0YiIGQ9Ik0xMDguNSwxMDdjLTE2LDIuNC0yMS43LDctMjAuNSwxNC4yYzIsMTEuOCwzOS43LDEwLjUsNDAuOSwwLjZDMTI5LjksMTEzLjMsMTE0LjgsMTA2LjEsMTA4LjUsMTA3eiIvPgoJCTxwYXRoIGQ9Ik05OC4yLDExMS44Yy0yLjcsOS44LDIxLjcsOC4zLDIxLjEsMmMtMC4zLTMuNy0zLjYtOC40LTEyLjMtOC4yQzEwMy42LDEwNS43LDk5LjQsMTA3LjIsOTguMiwxMTEuOHoiLz4KCQk8cGF0aCBmaWxsPSIjRTBERURDIiBkPSJNOTksMTI3LjdjLTAuOSwwLjQtMi40LDEwLjIsMi4yLDEwLjdjMy4xLDAuMywxMS42LDEuMywxMy42LDBjMy45LTIuNSwzLjUtOC41LDEuMy0xMAoJCQlDMTEyLjQsMTI2LDEwMCwxMjcuMiw5OSwxMjcuN3oiLz4KCTwvZz4KCTxnPgoJCTxwYXRoIGZpbGw9IiNFMERFREMiIGQ9Ik03My42LDU0LjNjMzMuNCwzLjksMjguOSwzOC43LDE2LDUwYy0yNCwyMS00OSwwLTQ2LjItMjEuMkM0Niw2Mi43LDYzLjMsNTMuMSw3My42LDU0LjN6Ii8+CgkJPGNpcmNsZSBmaWxsPSIjMTExMjEyIiBjeD0iNzEuNCIgY3k9Ijg0LjMiIHI9IjExLjQiLz4KCQk8Y2lyY2xlIGZpbGw9IiNGRkZGRkYiIGN4PSI3NC40IiBjeT0iNzkuNCIgcj0iMy42Ii8+Cgk8L2c+Cgk8cGF0aCBmaWxsPSIjQjg5MzdGIiBkPSJNMTkzLjYsMTg2LjdjMTEsMC4xLDUuNi0yMy41LTEuMi0xOC44Yy0zLjMsMi4zLTMuOSw3LjYtMy45LDEyLjFDMTg4LjUsMTgyLjUsMTkwLjUsMTg2LjYsMTkzLjYsMTg2Ljd6Ii8+Cgk8cGF0aCBmaWxsPSIjQjg5MzdGIiBkPSJNMjMuMywxODYuN2MtMTEsMC4xLTUuNi0yMy41LDEuMi0xOC44YzMuMywyLjMsMy45LDcuNiwzLjksMTIuMUMyOC40LDE4Mi41LDI2LjQsMTg2LjYsMjMuMywxODYuN3oiLz4KCTxwYXRoIGZpbGw9IiNCODkzN0YiIGQ9Ik0xNzIuNywyNTkuMmMtNi04LjktMTEuNC0yLTIwLjEsMi40Yy00LjEsMi4xLDYuOCw5LjYsMTksNEMxNzQuOCwyNjQuMSwxNzQuNywyNjIuMSwxNzIuNywyNTkuMnoiLz4KCTxwYXRoIGZpbGw9IiNCODkzN0YiIGQ9Ik00NC4yLDI2MC4yYzYtOC45LDExLjQtMiwyMC4xLDIuNGM0LjEsMi4xLTYuOCw5LjYtMTksNEM0Mi4xLDI2NS4xLDQyLjIsMjYzLjEsNDQuMiwyNjAuMnoiLz4KCTxwYXRoIGZpbGw9IiMzQzg5QkYiIGQ9Ik0xODguNiw0N2MtMC42LDIuMSwyLjEsMS44LDMuMSw4LjNjMC40LDIuNCw5LTMuNSw1LjUtNy44QzE5NC4zLDQzLjksMTg5LjEsNDQuOSwxODguNiw0N3oiLz4KCTxwYXRoIGZpbGw9IiMzQzg5QkYiIGQ9Ik0yOC4zLDQ3YzAuNiwyLjEtMi4xLDEuOC0zLjEsOC4zYy0wLjQsMi40LTktMy41LTUuNS03LjhDMjIuNSw0My45LDI3LjcsNDQuOSwyOC4zLDQ3eiIvPgo8L2c+Cjwvc3ZnPg==
  mediatype: image/svg+xml
