# GitLab

This project illustrates how to set up or install GitLab Server on Docker on your local machine.

In this project, I use Docker Desktop (version 25.0.3, build 4debf41) with WSL2 integration (Ubuntu 22.04.3 LTS) which also comes with Docker Compose (version 2.24.6-desktop.1)

Leveraging Docker, particularly Docker Compose, streamlines GitLab installation. Docker technology offers significant advantages, including efficient resource utilization (compute and memory), rapid startup times, and inherent lightweight portability across environments.

> Note: Following best practices to avoid any issues such as permission issues, I worked from the Linux file system instead of the windows file system (/mnt/)

### Setting up development workspace for Windows users

- Install the WSL extension in VS Code
- Open your WSL (Ubuntu) terminal and create a directory such as 'GitLab' in your account home directory (~/GitLab)
- Type ```code .``` to open Visual Studio code.
- Open the bash terminal from VS Code, make sure you are in the GitLab directory then type ```export GITLAB_HOME=$(pwd)``` to create an environment variable which will save the current working directory to be used by Docker compose.
- Type ```echo $GITLAB_HOME``` to verify if previous command worked.

### Setting up development workspace for Linux (Ubuntu) users

- Make sure you have similar or later Docker version installed which may also come with Docker Compose V2. If not, manually install Docker Compose V2.
- Create a directory such as 'GitLab' in your account home directory (~/GitLab) and check into it.
- Type ```export GITLAB_HOME=$(pwd)``` to create an environment variable which will save the current working directory to be used by Docker compose.
- Type ```echo $GITLAB_HOME``` to verify if previous command worked.

### Docker Compose yaml file

Create a ***docker-compose.yml*** file in the *~/GitLab* directory and paste this inside.

```version: '3.6'
services:
  gitlab:
    image: gitlab/gitlab-ce:latest
    container_name: gitlab
    restart: always
    hostname: gitlab-local
    environment:
      GITLAB_OMNIBUS_CONFIG: |
        # Add any other gitlab.rb configuration here, each on its own line
        external_url 'http://your-url'
    ports:
      - '80:80'
      - '443:443'
      - '22:22'
    volumes:
      - '$GITLAB_HOME/config:/etc/gitlab'
      - '$GITLAB_HOME/logs:/var/log/gitlab'
      - '$GITLAB_HOME/data:/var/opt/gitlab'
    shm_size: '256m'
```

- ***image:*** We are pulling the latest version of the GitLab Community Edition container image from DockerHub.
- ***restart:*** This ensures the container automatically restarts if it crashes or stops.
- ***environment:***  This defines environment variables for the container. Here, it sets the GITLAB_OMNIBUS_CONFIG variable with a multi-line string. String contains GitLab configuration for external_url. Type ```ip addr show``` to find your ip address for your local machine and use as the external_url.
- ***ports:***  This maps ports on the host machine to ports inside the container.
- ***volumes:*** This defines persistent storage for the container. Here, we are mounting three directories from the host machine to directories inside the container.
- ***shm_size:*** This allocates shared memory size (256MB in this case) for the container.

After configuring the docker-compose.yml file, type ```docker compose up -d``` to run the container.

Wait for some few minutes, open your browser and type ```http://[your local machine ip address]```

On the webpage, you will log in with username **root**.

To get the password, run the command ```sudo docker exec -it gitlab grep 'Password:' /etc/gitlab/initial_root_password```

> Note: File containing password is deleted after 24 hours.

The image below shows the welcome page on login in.
![Gitlab Welcome Page](./images/Gitlab%20homepage.png)
