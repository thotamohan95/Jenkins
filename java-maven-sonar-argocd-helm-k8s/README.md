# End-to-End CI/CD Pipeline for a Java Application

**Technology stack:** Jenkins · Maven · JUnit · Mockito · SonarQube · Docker · Container Registry · Helm · Argo CD · Kubernetes

## 1. Project Overview

This project demonstrates an end-to-end CI/CD workflow for a Java application. Jenkins automates continuous integration activities, SonarQube evaluates code quality, Docker packages the application into a container image, and Argo CD deploys the desired application state to Kubernetes using Helm and GitOps principles.

The workflow is designed around a clear separation of responsibilities:

- **Jenkins:** source checkout, build, tests, quality checks, image build, and image publishing.
- **Container registry:** stores versioned application images.
- **GitOps repository:** stores Helm chart configuration and environment-specific deployment values.
- **Argo CD:** reconciles the Kubernetes environment with the desired state declared in Git.
- **Kubernetes:** runs and manages the containerized application.

## 2. Architecture and Workflow

```text
Developer
   |
   v
Git Repository (Application Source)
   |
   v
Jenkins CI Pipeline
   |-- Checkout
   |-- Maven Build
   |-- Unit Tests (JUnit / Mockito)
   |-- SonarQube Analysis + Quality Gate
   |-- Package JAR
   |-- Build Docker Image
   |-- Push Image to Registry
   |
   v
Update Image Tag in GitOps Repository
   |
   v
Argo CD Detects Git Change
   |
   v
Helm Chart + Environment Values
   |
   v
Kubernetes Deployment
   |
   v
Smoke Tests / Health Validation / Monitoring
```

## 3. Prerequisites

Before implementing the pipeline, ensure the following are available:

- A Java application hosted in Git.
- Jenkins with an appropriately configured agent.
- A supported JDK and Maven installation on the build agent.
- SonarQube server and Jenkins integration.
- Docker engine or another supported image-building setup.
- Access to a container registry, such as Docker Hub or Amazon ECR.
- A Kubernetes cluster and appropriate access configuration.
- Helm installed in the environment where chart validation or related commands run.
- Argo CD installed and connected to the GitOps repository.
- Git credentials, registry credentials, and SonarQube credentials configured securely in Jenkins.

Exact versions and installation steps depend on the application, Jenkins agent, and Kubernetes environment.

## 4. Pipeline Stages

### Stage 1 — Source Checkout

Jenkins checks out the requested branch or commit from the application repository. Use a version-controlled `Jenkinsfile` to maintain the pipeline as code.

### Stage 2 — Build

Compile the application with Maven, for example:

```bash
mvn clean compile
```

### Stage 3 — Unit Tests

Execute automated unit tests using JUnit and Mockito:

```bash
mvn test
```

Publish JUnit test reports in Jenkins so failures and test trends are visible.

### Stage 4 — SonarQube Analysis and Quality Gate

Run static code analysis using the SonarQube Scanner for Maven or the project's configured scanner. Configure the SonarQube Quality Gate and make the pipeline fail when the required quality criteria are not met.

The exact Maven command depends on the SonarQube version and project configuration.

### Stage 5 — Package the Application

Create the deployable JAR:

```bash
mvn package
```

Configure the build to retain test results and avoid unintentionally skipping tests during packaging.

### Stage 6 — Build and Publish the Container Image

Build a Docker image using the application's Dockerfile. Tag it with an immutable identifier, such as the Git commit SHA or a release version, and push it to the configured registry.

Example commands (replace the placeholders with your actual values):

```bash
docker build -t <registry>/<image-name>:<version> .
docker push <registry>/<image-name>:<version>
```

Do not commit registry credentials or other secrets to source control.

### Stage 7 — Update the GitOps Repository

After CI succeeds, update the image tag in the relevant Helm values file in the GitOps repository. Teams may use a direct commit or a pull request, depending on their review and release policies.

For production, prefer an approved promotion process and an immutable image reference.

### Stage 8 — Deploy Through Argo CD

Argo CD monitors the configured Git repository and reconciles the target Kubernetes environment to match the declared configuration. The application is defined using a Helm chart and environment-specific values.

Jenkins may optionally request an Argo CD sync or check deployment status through its CLI/API. Keep deployment credentials in a secure credential store. Avoid using both Jenkins and Argo CD as competing deployment controllers for the same resources.

### Stage 9 — Validate and Monitor

After deployment, perform the checks required by the environment:

- Verify Kubernetes Deployment and Pod health.
- Confirm readiness and liveness probes are passing.
- Validate Service and Ingress connectivity, where configured.
- Review application logs and Kubernetes events.
- Run smoke, integration, API, or user acceptance tests as appropriate.
- Monitor application metrics and alerts using tools such as Prometheus and Grafana, if configured.

Promote to production only after the required tests and approvals pass.

## 5. Helm Chart Structure

A typical chart may use the following structure:

```text
java-app-chart/
├── Chart.yaml
├── values.yaml
└── templates/
    ├── deployment.yaml
    ├── service.yaml
    ├── ingress.yaml
    └── configmap.yaml
```

The actual resources depend on the application's networking and configuration needs. Keep environment-specific values in separate files, for example:

```text
values-dev.yaml
values-staging.yaml
values-prod.yaml
```

Configure resource requests and limits, probes, and non-sensitive application settings. Manage secrets using an approved secrets-management solution rather than storing plaintext credentials in Git.

## 6. Jenkins Plugins and Integrations

Install only the plugins needed for the chosen pipeline implementation. Common integrations include:

- Pipeline
- Git
- SonarQube Scanner for Jenkins
- JUnit
- Docker Pipeline, if the pipeline uses Jenkins Docker steps
- Maven Integration, if required by the job and build configuration

JUnit and Mockito are Java testing libraries/dependencies, not Jenkins plugins required to execute tests. Maven normally runs them as part of the application build.

A Kubernetes Continuous Deploy plugin is not required when Argo CD is the deployment controller.

## 7. Security and Release Practices

- Store tokens and credentials in Jenkins Credentials or an approved secrets manager.
- Use least-privilege access for Jenkins agents, registry accounts, and Kubernetes identities.
- Never hardcode passwords, tokens, or private keys in the `Jenkinsfile`, Dockerfile, Helm values, or Git history.
- Tag images with unique, traceable versions; avoid relying on `latest` for controlled releases.
- Apply a SonarQube Quality Gate and required automated tests before publishing a release.
- Use pull-request review or explicit approval for production configuration changes.
- Define and test rollback procedures before production releases.

## 8. Rollback Strategy

Use a controlled rollback process based on a previously validated image version and the GitOps configuration. Revert the relevant GitOps commit or restore the previous approved image tag, then allow Argo CD to reconcile the desired state. Verify application health after rollback.

## 9. Expected Outcome

The resulting workflow provides repeatable builds, automated testing, code-quality enforcement, versioned container artifacts, and GitOps-based Kubernetes delivery. It improves release traceability by linking application commits, build results, image versions, and deployment configuration.

## 10. Implementation Notes

This README describes the target workflow and architecture. The actual pipeline requires environment-specific configuration, including server URLs, credentials, repository paths, registry names, Kubernetes namespaces, Helm values, and SonarQube project settings. Replace placeholders and validate each integration before using the workflow in production.
