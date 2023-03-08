# github-workflows <a href="https://github.com/20treeAI/github-workflows/releases"><img src="https://img.shields.io/github/v/release/20treeAI/github-workflows?style=plastic&labelColor=484848&color=3CA324&logo=GitHub&logoColor=white"></a>

This repo contains [reusable workflows](https://docs.github.com/en/actions/learn-github-actions/reusing-workflows) for Github Actions.

## Dagster
This [workflow](./.github/workflows/dagster.yml) will build a docker image and then test it before pushing it.

[Example call to dagster workflow](./examples/dagster.yml)

<details>
  <summary>Workflow Input Variables</summary>

| name         | description                                                        | type   | default        | required | 
|:------------:|:-------------------------------------------------------------------|:------:|:---------------|:--------:|
| image_name   | Docker image name                                                  | string | None           | true     |
| branch       | Git branch used for tagging incremental builds of the Docker image | string | master         | true     |
| gcp_project  | GCP project where GCR is located for storing built Docker images   | string | None           | true     |
| cluster_name | K8s cluster name on which Dagster workflow is deployed to          | string | None           | true     |

#### Input Secrets
These are the GitHub repo secrets you must create ahead of time!

| name                      | description                                                | required | 
|:-------------------------:|:-----------------------------------------------------------|:--------:|
| SSH_KEY                   | SSH key used to access private repos during the build      | true     |
| GCR_RW_SERVICEACCOUNT_KEY | GCR service account credentials to push/pull Docker images | true     |

</details>


## Docker Build and Push (To GCR)
This [workflow](./.github/workflows/docker_build_push.yml) will build and push a docker image. You can optionally pass in artifacts from previous jobs with the `artifacts_object_name` and `artifacts_path` input variables, to ensure the docker image gets built with context from a previous job.

[Example call to Docker Build and Push workflow without artifacts](./examples/docker_build_push.yml)

<details>
  <summary>Workflow Input Variables</summary>

| name                  | description                                                        | type   | default  | required |
|:---------------------:|:-------------------------------------------------------------------|:------:|:---------|:--------:|
| image_name            | Docker image name                                                  | string | None     | true     |
| branch                | Git branch used for tagging incremental builds of the Docker image | string | main     | true     |
| gcp_project           | GCP project where GCR is located for storing built Docker images   | string | None     | true     |
| artifacts_object_name | Name of the artifacts object to pass to docker build job           | string | None     | false    |
| artifacts_path        | Path to use for the artifacts object                               | string | `build/` | false    |

        
#### Input Secrets
These are the GitHub repo secrets you must create ahead of time!

| name                      | description                                                | required | 
|:-------------------------:|:-----------------------------------------------------------|:--------:|
| SSH_KEY                   | SSH key used to access private repos during the build      | true     |
| GCR_RW_SERVICEACCOUNT_KEY | GCR service account credentials to push/pull Docker images | true     |

</details>


## Deploy to Google Cloud Run (Optional: Create Sentry Release)
This [workflow](./.github/workflows/) will deploy a docker image to a Cloud Run service and optionally create a sentry release.

[Example call to cloudrun workflow](./examples/cloudrun_deploy_optional_sentry.yml)

<details>
  <summary>Workflow Input Variables</summary>

| name           | description                                                       | type    | default        | required | 
|:--------------:|:------------------------------------------------------------------|:-------:|:---------------|:--------:|
| gcp_project    | GCP project where GCR is located for storing built Docker images  | string  | None           | true     |
| region         | Region to deploy cloudrun app and docker image                    | string  | `europe-west4` | false    |
| image_name     | Docker image name                                                 | string  | None           | true     |
| image_tag      | Name of Tag for Docker image                                      | string  | None           | false    |
| service_name   | Name of service to update in Cloud Run                            | string  | None           | true     |
| sentry_release | Whether or not to create a Sentry release for the this project    | boolean | false          | false    |
| environment    | Environment to deploy to: stage or prod                           | string  | None           | true     |

#### Input Secrets
These are the GitHub repo secrets you must create ahead of time!

| name                                | description                                                | required  | 
|:-----------------------------------:|:-----------------------------------------------------------|:---------:|
| CLOUDRUN_DEPLOYER_SERVICEACCOUNT_KEY| GCP Service Account key for the cloud run deployer         | true      |
| SENTRY_AUTH_TOKEN                   | Token for sentry authentication                            | false     |

</details>


## Yarn Build and Test
This [workflow](./.github/workflows/yarn_build_test.yml) will build and test a yarn project.

[Example call to yarn build and test workflow](./examples/yarn_build_test.yml)

<details>
  <summary>Workflow Input Variables</summary>

#### Input Secrets
These are the GitHub repo secrets you must create ahead of time!

| name  | description                                                | required | 
|:-----:|:-----------------------------------------------------------|:--------:|
| TOKEN | Any sort of secret token needed at build time for yarn     | false    |

</details>
