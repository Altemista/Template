# How to contribute 

## Creating a template 
First, create a new branch from master.
Create a template file according to the specification listed in `README.md` in `/templates/`.
A complete example can be found in the `testing` branch under `/templates/hellogo.yaml`.

## Validate your template 
Before publishing your template, you can validate your template locally by starting a local version of the developer portal in docker.
```bash
docker run --rm -p 8080:8080 -e OPERATOR_BRANCH=<your-branch-name> -e OPERATOR_REPO=<repository-url> altemista/dev-portal:latest
```
This will start a local version of the developer portal.
The environment variable `OPERATOR_REPO` is optional and only necessary if you forked the repository instead of creating a new branch.

When you update the template in your branch, you can either restart the container or make curl request to the webhook
```bash
curl -XPOST localhost:8080/api/webhook
```


> **_Attention_**: There is currently no option to test the CLI actions locally. For testing manually, you can follow these steps.
>1. Start an interactive docker container for the image specified in your template. If no image is specified use `altemista/altemista-cli`
>```bash
>docker run -it --rm <your-image> /bin/bash
>```
>2. Execute the commands listed in your template sequentially.
>3. Validate the results.
>4. If any errors occur, fix them and update your template accordingly. Go back to step one.


## Deploy your template
When everything looks and works as expected you can open a pull request to master.
If necessary squash your commits first to keep the commit history clean.