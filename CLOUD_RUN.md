# Cloud Run Deployment Mapping

This project currently uses GitHub Actions, Docker Hub, and Docker Compose
for its container CI/CD pipeline.

The same pipeline can be adapted to Google Cloud Run.

## Current Pipeline

1. A developer pushes code to the main branch.
2. GitHub Actions checks out the repository.
3. Docker Hub authentication is performed using GitHub Secrets.
4. The Docker image is built and tagged using the Git commit SHA.
5. The image is pushed to Docker Hub.
6. Docker Compose pulls the published image and deploys it.
7. A health check verifies the application.

## Google Cloud Run Mapping

| Current Pipeline | Google Cloud Equivalent |
|---|---|
| GitHub push | GitHub Actions push trigger |
| Docker Hub registry | Google Artifact Registry |
| docker/login-action | Google Cloud service account authentication |
| Docker image push | Push image to Artifact Registry |
| docker compose up -d | gcloud run deploy |
| Local health check | Cloud Run service health verification |

## Cloud Run Deployment Flow

1. Configure a Google Cloud project and enable Cloud Run
   and Artifact Registry APIs.
2. Create a Docker repository in Artifact Registry.
3. Configure GitHub Actions authentication using a Google Cloud
   service account and Workload Identity Federation.
4. Grant the service account the required Artifact Registry Writer
   and Cloud Run deployment permissions.
5. Authenticate using google-github-actions/auth.
6. Configure Docker authentication for Artifact Registry using
   gcloud auth configure-docker.
7. Build and push the image using a commit SHA tag.

Example Artifact Registry image:

REGION-docker.pkg.dev/PROJECT_ID/REPOSITORY/app:COMMIT_SHA

Example Cloud Run deployment command:

gcloud run deploy app \
  --image REGION-docker.pkg.dev/PROJECT_ID/REPOSITORY/app:COMMIT_SHA \
  --region REGION \
  --platform managed \
  --allow-unauthenticated

## Authentication and Security

- Use Workload Identity Federation instead of storing service account
  JSON keys in GitHub.
- Store required configuration in GitHub repository variables or secrets.
- Never hardcode credentials in workflow files.
- Grant only the permissions required by the deployment process.

## Scope

This document describes a possible Cloud Run integration only.
No Google Cloud resources have been created and no Cloud Run deployment
has been performed as part of this assignment.
<!-- No change only for re run the application -->