# github-workflows

This repo contains [reusable workflows](https://docs.github.com/en/actions/learn-github-actions/reusing-workflows) for Github Ections.

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
These are the github repo secrets you must create ahead of time

| name                      | description                                                | required | 
|:-------------------------:|:-----------------------------------------------------------|:--------:|
| SSH_KEY                   | SSH key used to access private repos during the build      | true     |
| GCR_RW_SERVICEACCOUNT_KEY | GCR service account credentials to push/pull Docker images | true     |

</details>


## Docker Build and Push
This [workflow](./.github/workflows/docker_build_push.yml) will build and push a docker image.

TODO: [Example call to Docker Build and Push workflow](./examples/)

<details>
  <summary>Workflow Input Variables</summary>

| name         | description                                                        | type   | default     | required | 
|:------------:|:-------------------------------------------------------------------|:------:|:------------|:--------:|
| image_name   | Docker image name                                                  | string | None        | true     |
| branch       | Git branch used for tagging incremental builds of the Docker image | string | master      | true     |
| gcp_project  | GCP project where GCR is located for storing built Docker images   | string | None        | true     |

#### Input Secrets
These are the github repo secrets you must create ahead of time

| name                      | description                                                | required | 
|:-------------------------:|:-----------------------------------------------------------|:--------:|
| SSH_KEY                   | SSH key used to access private repos during the build      | true     |
| GCR_RW_SERVICEACCOUNT_KEY | GCR service account credentials to push/pull Docker images | true     |

</details>


## Deploy to Cloud Run (Optional: Create Sentry Release)
This [workflow](./.github/workflows/) will deploy a docker image to a Cloud Run service and optionally create a sentry release.

TODO: [Example call to cloudrun workflow](./examples/)

<details>
  <summary>Workflow Input Variables</summary>

| name           | description                                                       | type    | default        | required | 
|:--------------:|:------------------------------------------------------------------|:-------:|:---------------|:--------:|
| image_name     | Docker image name                                                 | string  | None           | true     |
| gcp_project    | GCP project where GCR is located for storing built Docker images  | string  | None           | true     |
| cluster_name   | K8s cluster name on which Dagster workflow is deployed to         | string  | None           | true     |
| cloud_run      | Whether or not to deploy to Google Cloud Run                      | boolean | true           | true     |
| service_name   | Name of service to update in Cloud Run                            | string  | None           | true     |
| sentry_release | Whether or not to create a Sentry release for the this project    | boolean | false          | true     |
| environment    | Environment to deploy to: stage or prod                           | string  | None           | true     |

#### Input Secrets
These are the github repo secrets you must create ahead of time

| name                      | description                                                | required | 
|:-------------------------:|:-----------------------------------------------------------|:--------:|
| SSH_KEY                   | SSH key used to access private repos during the build      | true     |
| GCR_RW_SERVICEACCOUNT_KEY | GCR service account credentials to push/pull Docker images | true     |

</details>


## Yarn Build and Test
This [workflow](./.github/workflows/yarn_build_test.yml) will build and test a yarn project.

TODO: [Example call to yarn build and test workflow](./examples/)
