# Jenkins Setup and Configuration

This document provides detailed instructions on how to set up and configure Jenkins for this project.

## 1. Running Jenkins

This project includes a `docker-compose.yml` file to easily run Jenkins in a Docker container. To start Jenkins, run the following command from the root of the project:

```bash
docker-compose up -d
```

This will start a Jenkins instance and make it available at `http://localhost:8080`.

## 2. Initial Jenkins Setup

1.  **Unlock Jenkins**: When you first access Jenkins, you will be asked to unlock it with an initial administrator password. You can get this password from the logs of the Jenkins container:
    ```bash
    docker-compose logs jenkins
    ```
2.  **Install Suggested Plugins**: After unlocking Jenkins, you can install the suggested plugins.
3.  **Create First Admin User**: Create your own admin user account.

## 3. Installing Necessary Plugins

Navigate to **Manage Jenkins > Manage Plugins > Available** and install the following plugins:

*   Docker
*   Docker Pipeline
*   Kubernetes
*   GitLab

## 4. Adding Credentials

You need to add two sets of credentials to Jenkins:

1.  **Docker Hub Credentials**:
    *   Navigate to **Manage Jenkins > Manage Credentials > System > Global credentials (unrestricted)**.
    *   Click **Add Credentials**.
    *   **Kind**: Username with password
    *   **Scope**: Global
    *   **Username**: Your Docker Hub username
    *   **Password**: Your Docker Hub password
    *   **ID**: `dockerhub-credentials`

2.  **Kubernetes Configuration**:
    *   Navigate to **Manage Jenkins > Manage Credentials > System > Global credentials (unrestricted)**.
    *   Click **Add Credentials**.
    *   **Kind**: Secret file
    *   **Scope**: Global
    *   **File**: Upload your `kubeconfig` file for your EKS cluster.
    *   **ID**: `kubeconfig`

## 5. Creating the Pipeline

1.  On the Jenkins dashboard, click **New Item**.
2.  Enter a name for your pipeline (e.g., `proj1-pipeline`).
3.  Select **Pipeline** and click **OK**.
4.  In the pipeline configuration page, go to the **Pipeline** section.
5.  **Definition**: Select **Pipeline script from SCM**.
6.  **SCM**: Select **Git**.
7.  **Repository URL**: Enter the URL of your GitLab repository (e.g., `https://gitlab.com/vibecoe/proj1.git`).
8.  **Script Path**: Keep the default `Jenkinsfile`.
9.  Click **Save**.

## 6. Configuring the GitLab Webhook

1.  **In Jenkins**:
    *   Go to your pipeline configuration.
    *   Under **Build Triggers**, check **GitLab webhook trigger**.
    *   Copy the GitLab webhook URL.

2.  **In GitLab**:
    *   Go to your project's **Settings > Webhooks**.
    *   Paste the Jenkins webhook URL in the **URL** field.
    *   Select the **Push events** trigger.
    *   Click **Add webhook**.

Now, whenever you push new code to your GitLab repository, Jenkins will automatically trigger the pipeline.
