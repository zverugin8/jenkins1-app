#  DevOps BootCamp: CI/CD

# DevOps_CI_CD_course

## What is CI/CD?

CI/CD stands for Continuous Integration/Continuous Deployment. It refers to a software engineering practice that aims to automate and optimize the process of building, testing, and deploying code changes.

In a CI/CD workflow, developers integrate their code changes frequently, usually several times a day. Every time a change is made and committed to the code repository, a pipeline of automated build, test, and deployment tasks is triggered. The pipeline checks the integrity of the code, runs tests to ensure that it's working as expected, and then deploys the code to production if all the tests pass.

The goal of CI/CD is to enable faster and more reliable code releases by automating and streamlining the build, test, and deployment process. It helps to catch and fix errors early in the development cycle, and it makes it easier for developers to collaborate and work on code changes in parallel.

CI/CD is often used in conjunction with version control systems (such as Git), code repositories (such as GitLab), and continuous integration (CI) and deployment (CD) tools (such as Jenkins or GitLab CI). These tools help to automate and manage the various tasks involved in the CI/CD process.

![Alt text](/pictures/CICDBlog.png)

To implement CI/CD in your development workflow, you'll need to use a combination of tools and processes, such as:

A version control system, such as Git, to manage and track code changes
A code repository, such as GitLab, to host your code and manage the CI/CD process
A continuous integration (CI) and deployment (CD) tool, such as Jenkins or GitLab CI, to automate and manage the various tasks involved in the CI/CD process
Scripts and configuration files, such as a .gitlab-ci.yml file, to define the tasks and steps involved in the CI/CD process
To learn more about CI/CD and how to implement it in your development workflow, you can refer to online documentation and tutorials, such as the following resources:

- The GitLab documentation on CI/CD: https://docs.gitlab.com/ee/ci/

## Here are some tasks that could be included in a CI(Continuous Integration) course using GitLab:

- [x] You'll need to create a new repository on your personaly GitLab account and push the code from the GitHub repository https://github.com/benc-uk/python-demoapp to the new your GitLab repository.

- [x]  Setting up a GitLab account and setting up a GitLab account and creating a token. Use a Personal Access Token instead of a password. 

```text
Click on your avatar in the top right corner of the page, and then click on "Settings."
In the left sidebar, click on "Access Tokens."
In the "Name" field, give your token a name (e.g. "my-token").
In the "Expires at" field, select a date and time when the token should expire (optional).
Select the scope of the token.
Click on the "Create personal access token" button.
Make note of the token that is generated, as it will not be shown again.
```

- [x] set up a build pipeline in GitLab that will automatically **build**, **test**, and **push** packages to the GitLab container registry. The build image should be named using variables such as **PROJECT_NAMESPACE**, **CI_PROJECT_NAME**, **CI_REGISTRY** and **IMAGE_TAG**. Image's tag should be "v1.0.0"

Here is a list of some common environment variables that are available in a GitLab CI pipeline:

```text
$CI - indicates that the pipeline is running in GitLab CI.
$CI_COMMIT_SHA - the commit SHA of the current build.
$CI_COMMIT_REF_NAME - the branch or tag name of the current build.
$CI_REGISTRY - the URL of the container registry that is configured for the project.
$CI_REGISTRY_USER - the username for logging in to the container registry.
$CI_REGISTRY_PASSWORD - the password for logging in to the container registry.
$CI_JOB_TOKEN - a token that can be used to authenticate with the container registry.
$CI_PROJECT_NAME - the name of the project that is being built.
$CI_PROJECT_ID - the ID of the project that is being built.
$CI_PROJECT_DIR - the directory where the project is located on the runner's file system.
$CI_ENVIRONMENT_NAME - the name of the environment that is being deployed, if the pipeline is deploying to an environment.
These are just some examples and GitLab CI provides many more variables depending on the context and environment of your 
pipeline, you can find more information in the official documentation of GitLab:
https://docs.gitlab.com/ee/ci/variables/predefined_variables.html
```

It's worth mentioning that you can also define your own custom variables in your GitLab project settings, under **"CI/CD" > "Variables"**

- [x] Configuring GitLab to run my build pipeline on a regular schedule or when triggered by code changes. The pipeline should include steps to build, test and push the code with an image tag of **"v1.0.0"**. 

![Semantic description of image](/pic/ci.png "Image Title")

Here is an example of a .gitlab-ci.yml file that demonstrates how to set up a pipeline that builds and tests a push.

```yaml
variables:
  PROJECT_NAMESPACE: my-namespace
  CI_PROJECT_NAME: my-project
  IMAGE_TAG: latest

stages:
  - test
  - build
  - push

build:
  stage: build
  script:
    - echo "stage build"
test:
  stage: test
  script:
    - echo "stage test"

deploy:
  stage: deploy
  script:
     - echo "stage deploy"
```