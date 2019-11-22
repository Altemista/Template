# Template
NextGen DevOps ADF Templates

## Template Specification

Templates (or *bricks*) are YAML files that specify the asset requirements and provide the user with parameters to customize the project scaffolding process.

### Main Definition

| Field Name        | Type                  | Description                                              |
| ----------------- | --------------------- | -------------------------------------------------------- |
| **name**          | String                | The name of the template.                                |
| **categories**    | Array of Strings      | The category to which the template belongs.              |
| **description**   | String                | A short description of the purpose of the template.      |
| **prerequisites** | Array of Dictionaries | (Optional) List of the template's asset requirements                |
| **version**       | VersionString         | The version number of the template.                      |
| **license**       | String                | (Optional) Name of the license used for the template.               |
| **parameters**    | Array of Dictionaries | (Optional) The parameters for customizing the scaffolding process.  |
| **image**         | String                | (Optional) Name of a docker image, in which commands are executed.  |
| **commands**      | Array of Strings      | List of commands that are sequentially executed in a docker image. |
| **longDescription** | String              | (Optional) Markdown formated string, serving as description on the dev portal. |
| **displayName** | String | (Optional) Name shown on the developer portal. |
| **repository** | String | (Optional) Link for further information. |
| **icon** | Array of Dictionaries | (Optional) Containing base64 decoded icon data. |
| **provider** | String | Name of natural person or organization responsible for the template. |
| **maintainers** | Array of Dictionaries | (Optional) List of names and email addresses of people maintaining this template. |
| **links** | Array of Dictionaries | (Optional) Array of Strings | List of links for further information. |
| **createdAt** | String | (Optional) Datestring of creation, e.g. `2019-08-26T00:00:00Z`. |
| **replaces** | String | (Optional) Older version number that is replaced by this template. |

### Prerequisite Definition 

The prerequisites are a list of of dictionaries.

| Field Name      | Type   | Description                                        |
| --------------- | ------ | -------------------------------------------------- |
| **name**        | String | The name of the requirement.                       |
| **type**        | String | The type of requirement, e.g. software, asset, ... | 
| **description** | String | A short description of the requirement.            | 
| **link**        | String | Link to the required resource.                     |

### Parameter Definition

Variable informations can be passed to the executable argument in form of a list of dictionaries.

| Field Name       | Type   | Description                                                  |
| ---------------- | ------ | ------------------------------------------------------------ |
| **name**         | String | The name of the parameter.                                   |
| **type**         | String | Type of the parameter, `input` or `list`.                        |
| **message**      | String | Short description of the parameter, (optionally) presented to the user in the UI. |
| **defaultValue** | String | The default value of the parameter, autofilled in the UI. Only if type == input.  |
| **choices**      | Array of Strings | List of options to choose from. Only if type == list. |

### Icon Definition 

| Field Name     | Type   | Description                              |
| -------------- | ------ | ---------------------------------------- |
| **base64data** | String | Base64 encoded image data.               |
| **mediatype**  | String | Filetype of image, e.g. `image/svg+xml`. |

### Maintainer Definition 

| Field Name | Type   | Description                   |
| ---------- | ------ | ----------------------------- |
| **name**   | String | Name of maintainer.           |
| **email**  | String | E-Mail address of maintainer. |

### Link Definition

| Field Name | Type   | Description   |
| ---------- | ------ | ------------- |
| **name**   | String | Name of link. |
| **url**    | String | URL of link.  |



### Example Template

An example template file in YAML format for scaffolding a Spring project:

```yaml
name: quarkus
displayName: Quarkus Template
categories: 
  - Website
  - Catalog
prerequisites:
  - name: Java
    type: Software
    description: Java JDK must be installed
    link: http://oracle.com
  - name: Maven
    type: Software
    description: Maven Engine must be installed
    link: http://maven.com
version: 0.0.1
license: MIT
description: Brick to create a new project based on quarkus.
repository: 'https://github.com/apache/maven.git'
provider: 'Everis'
createdAt: '2019-10-16T00:00:00Z'
maintainers:
  - name: 'Everis'
    email: Alberto Cortes Cabello <alberto.cortes.cabello@everis.com>

image: maven:3.6.2-jdk-8
commands:
- mvn io.quarkus:quarkus-maven-plugin:0.19.1:create -DprojectGroupId=$groupId -DprojectArtifactId=$artifactId -DclassName=$className -Dpath=$path

parameters:
- name: groupId
  type: input
  message: "Group id for creating the artifact"
  default: com.example
- name: artifactId
  type: input
  message: Artifact id for creating the artifact
  default: getting-started
- name: className
  type: input
  message: Class name for creating the initial className
  default: org.acme.quickstart.GreetingResource
- name: path
  type: input
  message: Path for the initial endpoint
  default: "/hello"
 ```
