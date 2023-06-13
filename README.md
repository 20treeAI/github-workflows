# github-workflows <a href="https://github.com/20treeAI/github-workflows/releases"><img src="https://img.shields.io/github/v/release/20treeAI/github-workflows?style=plastic&labelColor=484848&color=3CA324&logo=GitHub&logoColor=white"></a>

This repo contains [reusable workflows](https://docs.github.com/en/actions/learn-github-actions/reusing-workflows) for Github Actions.

## Dagster Build, Test, Deploy

This [workflow](./.github/workflows/dagster.yml) will build a docker image and then test it before pushing it to GCR. Also supports creating development environment in stage cluster if you label a PR `dev-env`. The development enviornment will be removed if you remove the label or close the PR.

[Example call to dagster workflow](./examples/dagster.yml)

<details>
  <summary>Workflow Input Variables</summary>

| name                               | description                                                                     |  type  | default          | required |
| :--------------------------------- | :------------------------------------------------------------------------------ | :----: | :--------------- | :------: |
| image_name                         | Docker image name                                                               | string | None             |   true   |
| branch                             | Git branch used for tagging incremental builds of the Docker image              | string | main             |  false   |
| docker_buildx_driver               | Driver to use for docker buildx. Set to "docker" if needed.                     | string | docker-container |  false   |
| gcp_project                        | GCP project where GCR/GKE are located for storing/deploying built Docker images | string | None             |   true   |
| gcp_location                       | Location where GKE is located for storing built Docker images                   | string | europe-west4     |  false   |
| cluster_name                       | K8s cluster name on which Dagster jobs are deployed to                          | string | None             |   true   |
| stage_cluster_name                 | K8s stage cluster name on which Dagster jobs are deployed to                    | string | None             |   true   |
| stage_cluster_domain               | FQDN for URL for cluster running dagster                                        | string | None             |   true   |
| stage_auth_domain                  | FQDN for authentication URL for cluster running dagster                         | string | None             |   true   |
| stage_dagster_service_account_name | Development K8s cluster name on which Dagster jobs are deployed to              | string | None             |   true   |
| dagster_version                    | Version of dagster to deploy helm chart for                                     | string | '0.15.10'        |  false   |

</details>
  
## Dagster Scheduled Rebuild and Deploy

This [workflow](./.github/workflows/dagster-scheduled-workflow.yml) will build a docker image and then test it before pushing it to GCR first in stage and then will retag for production.

[Example call to dagster workflow](./examples/dagster_nightly_rebuild.yml)

<details>
  <summary>Workflow Input Variables</summary>

| name                     | description                                                                     |  type  | default          | required |
| :----------------------- | :------------------------------------------------------------------------------ | :----: | :--------------- | :------: |
| image_name               | Docker image name                                                               | string | None             |   true   |
| docker_buildx_driver     | Driver to use for docker buildx. Set to "docker" if needed.                     | string | docker-container |  false   |
| gcp_project              | GCP project where GCR/GKE are located for storing/deploying built Docker images | string | None             |   true   |
| gcp_location             | Location where GKE is located for storing built Docker images                   | string | europe-west4     |  false   |
| cluster_name             | K8s cluster name on which Dagster jobs are deployed to                          | string | None             |   true   |
| stage_cluster_name       | K8s stage cluster name on which Dagster jobs are deployed to                    | string | None             |   true   |
| prod_github_environment  | The prod GitHub environment you'd like to use for deployments                   | string | None             |   true   |
| stage_github_environment | The stage GitHub environment you'd like to use for deployments                  | string | None             |   true   |

#### Input Secrets

These are the GitHub repo secrets you must create ahead of time!

| name                            | description                                                  | required |
| :------------------------------ | :----------------------------------------------------------- | :------: |
| SSH_KEY                         | SSH key used to access private repos during the build        |   true   |
| GCR_RW_SERVICEACCOUNT_KEY       | GCR service account credentials to push/pull Docker images   |   true   |
| DOCKER_BUILD_SERVICEACCOUNT_KEY | Service account credentials used when building Docker images |  false   |

</details>

## Docker Build and Push To GCR <small>(Optional: test with dagster)</small>

This [workflow](./.github/workflows/docker_build_push.yml) will build and push a docker image. You can optionally pass in artifacts from previous jobs with the `artifacts_object_name` and `artifacts_path` input variables, to ensure the docker image gets built with context from a previous job.

[Example call to Docker Build and Push workflow without artifacts](./examples/docker_build_push.yml)

<details>
  <summary>Workflow Input Variables</summary>

| name                  | description                                                                   |  type   | default          | required |
| :-------------------- | :---------------------------------------------------------------------------- | :-----: | :--------------- | :------: |
| image_name            | Docker image name                                                             | string  | None             |   true   |
| branch                | Git branch used for tagging incremental builds of the Docker image            | string  | main             |   true   |
| docker_buildx_driver  | Driver to use for docker buildx. Set to "docker" if needed.                   | string  | docker-container |  false   |
| gcp_project           | GCP project where GCR is located for storing built Docker images              | string  | None             |   true   |
| artifacts_object_name | Name of the artifacts object to pass to docker build job                      | string  | None             |  false   |
| artifacts_path        | Path to use for the artifacts object                                          | string  | `build/`         |  false   |
| test_dagster          | whether or not to test docker image for dagster compatibility                 | boolean | false            |  false   |
| skip_image_push       | whether to skip image push (so that you can test image build without pushing) | boolean | false            |  false   |

#### Input Secrets

These are the GitHub repo secrets you must create ahead of time!

| name                            | description                                                  | required |
| :------------------------------ | :----------------------------------------------------------- | :------: |
| SSH_KEY                         | SSH key used to access private repos during the build        |   true   |
| GCR_RW_SERVICEACCOUNT_KEY       | GCR service account credentials to push/pull Docker images   |   true   |
| DOCKER_BUILD_SERVICEACCOUNT_KEY | Service account credentials used when building Docker images |  false   |

</details>

## Deploy to Google Cloud Run (Optional: Create Sentry Release)

This [workflow](./.github/workflows/cloudrun_deploy_optional_sentry.yml) will deploy a docker image to a Cloud Run service and optionally create a sentry release.

[Example call to cloudrun workflow](./examples/cloudrun_deploy_optional_sentry.yml)

<details>
  <summary>Workflow Input Variables</summary>

| name           | description                                                      |  type   | default        | required |
| :------------- | :--------------------------------------------------------------- | :-----: | :------------- | :------: |
| gcp_project    | GCP project where GCR is located for storing built Docker images | string  | None           |   true   |
| region         | Region to deploy cloudrun app and docker image                   | string  | `europe-west4` |  false   |
| image_name     | Docker image name                                                | string  | None           |   true   |
| image_tag      | Name of Tag for Docker image                                     | string  | None           |  false   |
| service_name   | Name of service to update in Cloud Run                           | string  | None           |   true   |
| sentry_release | Whether or not to create a Sentry release for the this project   | boolean | false          |  false   |
| environment    | Environment to deploy to: stage or prod                          | string  | None           |   true   |

#### Input Secrets

These are the GitHub repo secrets you must create ahead of time!

| name                                 | description                                        | required |
| :----------------------------------- | :------------------------------------------------- | :------: |
| CLOUDRUN_DEPLOYER_SERVICEACCOUNT_KEY | GCP Service Account key for the cloud run deployer |   true   |
| SENTRY_AUTH_TOKEN                    | Token for sentry authentication                    |  false   |
| SENTRY_ORG                           | Sentry organisation for release tracking           |  false   |
| SENTRY_PROJECT                       | Sentry project for release tracking                |  false   |

</details>

## Yarn Build and Test

This [workflow](./.github/workflows/yarn_build_test.yml) will build and test a yarn project.

[Example call to yarn build and test workflow](./examples/yarn_build_test.yml)

<details>
  <summary>Workflow Input Variables</summary>

#### Input Secrets

These are the GitHub repo secrets you must create ahead of time!

| name                         | description                                             | required |
| :--------------------------- | :------------------------------------------------------ | :------: |
| REACT_APP_MAPBOX_TOKEN_STAGE | stage mapbox token secret needed at build time for yarn |  false   |
| REACT_APP_MAPBOX_TOKEN_PROD  | prod mapbox token secret needed at build time for yarn  |  false   |

</details>

## Deploy mkdocs via Github Pages

This [workflow](./.github/workflows/deploy_mkdocs.yml) will install python, poetry, and then deploy the docs dependency group.

You must already have a [docs dependency group](https://python-poetry.org/docs/managing-dependencies/#optional-groups) defined in your `pyproject.toml` like:

```toml
[tool.poetry.group.docs.dependencies]
mkdocs-material = "^8.5.1"
```

[Example call to mkdocs workflow](./examples/deploy_mkdocs.yml)

<details>
  <summary>Workflow Input Variables</summary>

| name           | description                      |  type  | default | required |
| :------------- | :------------------------------- | :----: | :------ | :------: |
| python_version | version of python you'd like use | string | '3.10'  |  false   |
| poetry_version | version of poetry you'd like use | string | '1.4.1' |  false   |

</details>

## Terraform Lint, Plan, Deploy to GCP

This [workflow](./.github/workflows/terraform.yml) will deploy a private terraform repo to GCP.

[Example call to terraform workflow](./examples/terraform.yml)

<details>
  <summary>Workflow Input Variables</summary>

|        name         | description                                                      |  type  | default | required |
| :-----------------: | :--------------------------------------------------------------- | :----: | :------ | :------: |
| terraform_workspace | The terraform workspace you'd like to plan and deploy changes to | string | None    |   true   |
| github_environment  | The GitHub environment you'd like to use for deployments         | string | None    |   true   |

#### Input Secrets

These are the GitHub repo secrets you must create ahead of time!

| name                              | description                                                                 | required |
| :-------------------------------- | :-------------------------------------------------------------------------- | :------: |
| SSH_KEY                           | SSH key used to access private repos during the build                       |   true   |
| GCP_TERRAFORM_SERVICE_ACCOUNT_KEY | service account credentials to deploy your terraform infra                  |   true   |
| TF_GITHUB_APP_ID                  | ID of App for authenticating via the Github Terraform provider              |  false   |
| TF_GITHUB_APP_INSTALLATION_ID     | Installation ID of App for authenticating via the Github Terraform provider |  false   |
| TF_GITHUB_APP_PEM_FILE            | PEM file of App for authenticating via the Github Terraform provider        |  false   |

</details>

## Run CI and publish Python library

This [workflow](./.github/workflows/python_library_ci.yml) will run precommit hooks and tests for a python project and publish the library.

[Example workflow call](./examples/python_library_ci.yml)

<details>
  <summary>Workflow Input Variables</summary>

|       name        | description                                                                        |  type   | default | required |
| :---------------: | :--------------------------------------------------------------------------------- | :-----: | :------ | :------: |
|     repo_uri      | Location of the python repository                                                  | string  | None    |   true   |
| use_release_name  | Whether to set package version as the Github release naem                          | boolean | false   |  false   |
|  python_version   | Python version to use when running CI                                              | string  | 3.10    |  false   |
|  poetry_version   | Poetry version to run and build package                                            | string  | 1.5.1   |  false   |
| working_directory | Working directory where source code is located. Default: current working directory | string  | .       |  false   |
|  conda_env_file   | If Conda is used then the Conda environment file                                   | string  | None    |  false   |

#### Input Secrets

|              name              | description                                            | required |
| :----------------------------: | :----------------------------------------------------- | :------: |
| REGISTRY_RW_SERVICEACCOUNT_KEY | Service account credentials to publish to the registry |   true   |
|    TEST_SERVICEACCOUNT_KEY     | Service account credentials to run the tests           |  false   |

</details>
