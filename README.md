# 🚀 Unified DevOps & CI/CD Documentation

This guide outlines the end-to-end process of setting up a Linux-based DevOps environment, containerizing applications with Docker, and automating deployment using Jenkins.

---

## 1. System Environment Setup

The foundation requires updating the system and installing core communication and version control tools.

- **System Update**: Ensure all packages are current:
  ```bash
  sudo apt update && sudo apt upgrade -y
SSH Configuration: Install and enable the OpenSSH server to allow remote management and firewall access.

Git Installation: Install Git and OpenSSH client to manage source code:

sudo apt install git openssh-client -y
Java Environment: Install OpenJDK (version 17 or 21), required for running Jenkins:

sudo apt install openjdk-17-jdk -y
2. Docker Infrastructure & Manual Deployment
Docker is used to package applications into portable containers.

Installation: Install Docker Engine and associated plugins (buildx, compose).

Permissions: Add the current user and Jenkins user to the docker group to run commands without sudo:

sudo usermod -aG docker $USER
sudo usermod -aG docker jenkins
Nginx Static Hosting: Quickly host static sites by mounting a local directory to an Nginx container:

docker run -d --name web-container -p 8080:80 -v $(pwd):/usr/share/nginx/html nginx:alpine
3. Application Preparation
Before automation, applications must be verified locally.

Node.js: Install the LTS version of Node.js and build tools:

sudo apt install nodejs npm build-essential -y
Local Build: Clone the repository, install dependencies, and test:

git clone <repository_url>
cd <repository_directory>
npm install
npm run build  # or npm start for development server
4. Jenkins CI/CD Pipeline Architectures
Two primary methods for automation were established.

Method A: Chained Freestyle Jobs
This approach uses three separate jobs that trigger one another in sequence:

Clone Job: Pulls the latest source code from the Git repository.

Build Image Job: Triggered by the Clone Job; builds the Docker image:

docker build -t app-image .
Run Container Job: Triggered by the Build Job; stops/removes old containers and starts a new one:

docker stop running-app
docker rm running-app
docker run -d -p 80:3000 --name running-app app-image
Method B: Unified Multi-Stage Pipeline
A single pipeline script handles the entire lifecycle, providing a "SUCCESS" or "FAILURE" result for the whole flow.

Stages:

Clean Workspace

Clone Repository

Verify Environment

Install / Build

Docker Image Build

Container Deployment

Deployment: The application will be accessible via the server IP on a designated port (e.g., port 3000).

5. Validation & Maintenance
Verification: Ensure the container status is "Up" and port mappings are correct:

docker ps
Logs: Review Jenkins console output to confirm "Finished: SUCCESS" for each pipeline run.

Cleanup: Manage container lifecycles during updates:

docker stop running-app
docker rm running-app
