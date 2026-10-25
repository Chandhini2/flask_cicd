# Flask Application – CI/CD Automation Using Jenkins and GitHub Actions

## 📌 Project Overview

This project demonstrates the implementation of Continuous Integration and Continuous Deployment (CI/CD) pipelines for a Python Flask application using **Jenkins** and **GitHub Actions**.

The pipelines automate dependency installation, application testing, build validation, and deployment workflows. They also support automated triggers and build notifications to improve software delivery and reliability.

## 🎯 Objectives

- Automate the Flask application's build and testing process.
- Implement CI/CD using Jenkins and GitHub Actions.
- Execute unit tests using `pytest`.
- Configure branch-based staging deployment.
- Configure production deployment using Git tags.
- Store deployment credentials securely using GitHub Secrets.
- Configure email notifications for Jenkins build results.
- Document pipeline execution and verification.

## 🛠️ Technologies Used

| Technology | Purpose |
|---|---|
| Python | Application programming language |
| Flask | Python web framework |
| Git and GitHub | Source code management |
| Jenkins | CI/CD pipeline automation |
| GitHub Actions | Cloud-based workflow automation |
| pytest | Automated unit testing |
| pip | Python dependency management |
| YAML | GitHub Actions workflow configuration |
| Jenkinsfile | Jenkins pipeline definition |

---

# Part 1: Jenkins CI/CD Pipeline

## 1. Objective

Create a Jenkins pipeline that automatically installs dependencies, runs tests, and deploys the Flask application to a staging environment after successful testing.

## 2. Prerequisites

- A GitHub account.
- Jenkins installed on a virtual machine or access to a cloud-based Jenkins service.
- Git installed on the Jenkins agent.
- Python 3 and pip installed on the Jenkins agent.
- A fork of the provided Flask repository.
- Jenkins credentials and email configuration, where required.

## 3. Source Code Repository

Original repository:

https://github.com/mohanDevOps-arch/flask_Practice.git

Fork the repository into your GitHub account and use your fork as the pipeline source.

Example repository URL:

`https://github.com/<your-username>/flask_Practice.git`

Replace the placeholder with your actual repository URL.

## 4. Jenkins Pipeline Architecture

```text
Developer
    |
    v
GitHub Repository
    |
    | Push to main branch
    v
Jenkins Pipeline Trigger
    |
    v
Build Stage
Install Python Dependencies
    |
    v
Test Stage
Execute pytest
    |
    v
Tests Passed?
    |
    v
Deploy to Staging
    |
    v
Pipeline Result
    |
    v
Email Notification
```

If the tests fail, the pipeline should stop before deployment and send a failure notification.

## 5. Jenkinsfile Configuration

Create a file named `Jenkinsfile` in the root directory of the repository.

The pipeline should contain the following stages:

### Build

- Check out the application source code.
- Create a Python virtual environment if supported by the agent.
- Install the required dependencies using `pip`.
- Install testing dependencies such as `pytest`.

### Test

- Execute the unit test suite.
- Capture the test results.
- Stop the pipeline if any tests fail.

Example command:

```bash
python -m pytest -v
```

### Deploy to Staging

- Execute this stage only if the tests pass.
- Deploy the application or package to the configured staging environment.
- Verify that the application is available after deployment.

**Note:** A deployment target and deployment commands must be configured before this stage can perform an actual deployment.

## 6. Configure Jenkins

1. Open the Jenkins dashboard.
2. Install the required plugins, including Pipeline, Git, and an email notification plugin if needed.
3. Configure the Git installation and Python environment on the Jenkins agent.
4. Create a new Pipeline job.
5. Configure the GitHub repository URL.
6. Select the appropriate branch, such as `main`.
7. Configure the pipeline to use the `Jenkinsfile` from source control.
8. Configure a GitHub webhook or another supported SCM trigger to initiate builds on pushes.
9. Save the job and run the pipeline.

## 7. Email Notifications

Configure Jenkins email settings with the appropriate SMTP server and credentials.

The pipeline should send notifications for:

- Successful builds.
- Failed builds.
- Failed tests or deployment stages.

Store credentials in Jenkins Credentials rather than hardcoding passwords or tokens in the Jenkinsfile.

## 8. Verify the Jenkins Pipeline

Confirm that:

- The source code is checked out successfully.
- Dependencies install without errors.
- Unit tests execute successfully.
- Deployment runs only after successful tests.
- A new build starts after a push to `main`.
- Email notifications are received.
- Failed tests prevent deployment.

---

# Part 2: GitHub Actions CI/CD Pipeline

## 1. Objective

Implement a GitHub Actions workflow that automates testing and build validation, deploys to staging when changes are pushed to the `staging` branch, and supports production deployment when a version tag is published.

## 2. Prerequisites

- A GitHub repository containing the Flask application.
- Python 3 and pip.
- A `requirements.txt` file listing the application dependencies.
- Unit tests compatible with `pytest`.
- A `main` branch and a `staging` branch.
- A configured staging and production deployment target.
- GitHub Actions enabled for the repository.

## 3. Repository Structure

The relevant project structure should resemble the following:

```text
flask_Practice/
│
├── app.py
├── requirements.txt
├── tests/
│   └── test_app.py
├── Jenkinsfile
├── .github/
│   └── workflows/
│       └── flask-ci-cd.yml
└── README.md
```

The application and test filenames may differ depending on the original repository. Adjust the workflow to match the actual project structure.

## 4. Create the GitHub Actions Workflow

Create the following directory and file in the repository:

`.github/workflows/flask-ci-cd.yml`

The workflow should implement these stages:

### Install Dependencies

- Check out the repository.
- Set up the required Python version.
- Install application and testing dependencies using `pip`.

### Run Tests

Execute the automated tests using:

```bash
python -m pytest -v
```

The workflow must stop if the tests fail.

### Build

Prepare the application for deployment after successful testing. This can include validating the source code, packaging the application, or building a deployment artifact.

### Deploy to Staging

Trigger staging deployment when changes are pushed to the `staging` branch, after successful build and testing.

### Deploy to Production

Trigger production deployment when a version tag, such as `v1.0.0`, is pushed. Production deployment must use the configured production target and protected credentials.

**Important:** GitHub Actions does not automatically deploy an application merely because a workflow contains a deployment stage. The actual deployment commands, server or platform details, and credentials must be configured for your chosen hosting environment.

## 5. Workflow Trigger Strategy

| Event | Expected action |
|---|---|
| Push to `main` | Run CI checks |
| Push to `staging` | Run CI checks and staging deployment |
| Push a version tag such as `v1.0.0` | Run CI checks and production deployment |

The workflow should ensure that testing and build validation succeed before either deployment proceeds.

## 6. Configure GitHub Secrets

Navigate to:

**GitHub Repository → Settings → Secrets and variables → Actions → New repository secret**

Create only the secrets required by your deployment target. Examples include:

| Secret name | Purpose |
|---|---|
| `STAGING_HOST` | Staging server hostname, if applicable |
| `STAGING_USER` | Staging deployment username, if applicable |
| `STAGING_SSH_KEY` | Private deployment key, if SSH is used |
| `PRODUCTION_HOST` | Production server hostname, if applicable |
| `PRODUCTION_USER` | Production deployment username, if applicable |
| `PRODUCTION_SSH_KEY` | Production deployment key, if SSH is used |

These are example names, not mandatory secrets. Use the appropriate values for your actual hosting platform.

Security recommendations:

- Never commit passwords, tokens, or private keys to GitHub.
- Reference secrets through the GitHub Actions `secrets` context.
- Grant deployment jobs only the permissions they need.
- Protect the production environment with approval rules where available.
- Never print secret values in workflow logs.

## 7. Branch Management

Create and maintain the required branches.

Example commands:

```bash
git checkout main
git pull origin main

git checkout -b staging
git push -u origin staging
```

If the `staging` branch already exists, use:

```bash
git checkout staging
git pull origin staging
```

Push changes to the appropriate branch to test the configured workflow triggers.

## 8. Production Release Tag

After validating the application and preparing a release, create and push a version tag.

Example:

```bash
git checkout main
git pull origin main

git tag v1.0.0
git push origin v1.0.0
```

Use a new version tag for each release. Confirm that the workflow's tag trigger matches the naming convention used by the repository.

---

# Part 3: Testing and Validation

## Jenkins Validation Checklist

- [ ] Jenkins job is configured correctly.
- [ ] GitHub repository checkout succeeds.
- [ ] Dependencies install successfully.
- [ ] Automated tests execute successfully.
- [ ] Staging deployment executes after successful tests.
- [ ] Pushes to `main` trigger new builds.
- [ ] Success and failure email notifications work.
- [ ] Jenkins console logs show the pipeline stages.

## GitHub Actions Validation Checklist

- [ ] Workflow YAML file is committed to the repository.
- [ ] `main` and `staging` branches exist.
- [ ] Dependencies install successfully.
- [ ] Unit tests pass.
- [ ] Build validation succeeds.
- [ ] A push to `staging` triggers the staging workflow.
- [ ] A version tag triggers the production workflow.
- [ ] Required deployment secrets are configured.
- [ ] Deployment completes successfully in the configured target.
- [ ] Workflow logs show successful execution.

---

# Part 4: Screenshots and Evidence

Capture genuine screenshots of your completed pipeline executions for assignment submission.

## Jenkins Screenshots

1. Jenkins dashboard showing the pipeline job.
2. Jenkins pipeline stage view showing Build, Test, and Deploy to Staging.
3. Successful build console output.
4. Test execution results.
5. Staging deployment output.
6. Email notification for a successful or failed build.

## GitHub Actions Screenshots

1. The `.github/workflows/flask-ci-cd.yml` file in the repository.
2. A successful workflow run showing dependency installation and tests.
3. The completed build stage.
4. A staging deployment run triggered by a push to `staging`.
5. A production workflow run triggered by a version tag.
6. Deployment logs showing successful execution.

**Note:** Capture actual results from your environment. Do not mark a stage as completed unless you have executed and verified it.

---

# Part 5: Troubleshooting

| Problem | Suggested solution |
|---|---|
| Jenkins cannot find Python | Install Python on the Jenkins agent and configure the executable path. |
| `pytest` command is not found | Install pytest in the same Python environment used by the pipeline. |
| `ensurepip` or virtual environment error | Install the appropriate Python virtual environment package for the operating system, or use a supported preconfigured environment. |
| Git checkout fails | Verify the repository URL, branch name, and credentials. |
| GitHub webhook does not trigger Jenkins | Verify the webhook URL, Jenkins connectivity, job trigger configuration, and webhook delivery logs. |
| GitHub Actions workflow does not run | Check the YAML syntax, branch names, tag filters, and workflow triggers. |
| Tests fail | Review the test output and application dependencies before attempting deployment. |
| Deployment fails | Check the deployment target, network access, permissions, secrets, and deployment commands. |
| Email notification fails | Verify SMTP settings, credentials, and Jenkins email plugin configuration. |

---

# Part 6: Project Outcomes

This project demonstrates practical experience with:

- CI/CD pipeline design and automation.
- Python dependency management.
- Automated testing and build validation.
- Jenkins declarative pipeline concepts.
- GitHub Actions workflow configuration.
- Git branching and release tagging.
- Secure handling of deployment credentials.
- Staging and production deployment strategies.
- Build monitoring, troubleshooting, and notification configuration.

## Conclusion

The project demonstrates two approaches to automating software delivery for a Flask application: Jenkins-based CI/CD and GitHub Actions-based CI/CD.

Both approaches use automated testing and build validation to reduce manual effort and help prevent untested code from reaching deployment environments. With the required deployment targets, credentials, and notifications configured, the pipelines can support a repeatable and secure application delivery process.

---

