# # Docker Concepts

# This guide offers a comprehensive overview of Docker, from the fundamental concepts to advanced techniques like multi-stage builds, security best practices, and variable management. It is intended to be a reference material for daily use and learning.

# ---

# ## Section one: Introduction to Docker
### Overview
Docker is a platform designed to help developers build, share, and run applications in containers. 
A container is a lightweight and portable unit that contains everything needed to run a software application.

# ### What are Containers?
Containers package applications and their dependencies together, ensuring they run reliably across different environments.
Unlike virtual machines, containers use the host system's OS kernel and are faster and more lightweight.

# - Containers are portable computing environments that package applications with all their dependencies, ensuring consistency across systems.
# - Unlike virtual machines, containers share the host OS kernel, making them more lightweight and efficient.

# ### Advantages of Containers
Containers offer improved performance and scalability. They help in creating consistent development, testing, and production environments.
Developers and data scientists use containers to easily share code and configurations.

# - **Portability**: Containers run the same way on any platform.
# - **Reproducibility**: Consistent environments across teams.
# - **Security**: Isolated execution environments.
# - **Lightweight**: Reduced overhead compared to virtual machines.

# ### Containers in Data Science
Containers simplify the replication of experiments and analysis. They include code, dependencies, and datasets in a single package,
allowing seamless collaboration and reproducibility across teams.

# - Include dependencies and datasets.
# - Reproducible experiments.
# - Shareable containers across teams.

# ---

# ## Section Two: Running Docker Containers
### Overview
This section introduces how to start, manage, and interact with containers using Docker's Command Line Interface (CLI).

# ### Prerequisites

# Basic shell commands:
# - `nano`, `touch`, `echo`, `ls`, `cd`, `mkdir`

# ### Docker CLI
Docker CLI is the command-line tool that communicates with the Docker daemon to execute container-related tasks.
Every Docker command begins with the keyword `docker`.

# - All Docker commands start with `docker`.

# ### Starting Containers
Use `docker run` to start containers from existing images. These images could be official Docker images or custom-built.

# ```bash
# #
docker run <image-name>      # Start a container
docker run hello-world       # Run hello-world test image
# ```

# ### Interactive Mode
Interactive mode allows users to interact with the container via the terminal using `-it` flags.

# ```bash
# #
docker run -it ubuntu         # Start interactive terminal
# ```

# ### Detached Mode
Detached mode (`-d`) runs containers in the background, letting you continue using the terminal.

# ```bash
# #
docker run -d postgres        # Run container in detached mode
# ```

# ### Managing Containers
After starting containers, you can list, stop, or remove them using commands such as `docker ps`, `docker stop`, and `docker container rm`.

# ```bash
# #
docker ps                      # List all running containers                      # List running containers
docker stop <container-id>     # Stop a running container     # Stop container
docker container rm <id>       # Remove a container       # Remove container
# ```

# ### Logs
You can view and monitor container logs using `docker logs`, including real-time logging with the `-f` option.

# ```bash
# #
docker logs <id>                # View logs of container
docker logs -f <id>            # Follow live logs            # Live logs
# ```

# ### Named Containers
Assigning names to containers helps in identifying and managing them easily. You can refer to containers by name instead of ID.

# ```bash
# #
docker run --name my_container <image>  # Run with a specific name
docker ps                      # List all running containers -f "name=my_container"
# ```

# ### Image Management
Docker images are the blueprints for containers. You can pull images from repositories or delete unused ones to save space.

# ```bash
# #
docker pull <image>              # Download image
docker pull <image>              # Download image:<version>
docker images                   # List all local images
docker image rm <image>         # Remove an image
# ```

# ### Cleanup
Use Docker’s prune commands to remove stopped containers and unused images, which helps maintain a clean working environment.

# ```bash
# #
docker container prune           # Remove all stopped containers
docker image prune -a             # Remove unused images
# ```

# ### Registry Interaction
Docker images can be stored in public or private registries. You can log in, tag, and push images to these registries for sharing or deployment.

# ```bash
# #
docker login <registry>          # Log in to a registry
docker tag local_image registry.com/image:tag  # Tag image for registry
docker push registry.com/image:tag   # Push image to registry
# ```

# ### Save and Load Images
When network sharing isn't possible, Docker allows exporting and importing images via `.tar` files using `docker save` and `docker load`.

# ```bash
# #
docker save -o image.tar my_image     # Save image as tar file
docker load -i image.tar           # Load image from tar file
# ```

# ---

# ## Section Three: Creating Docker Images
### Overview
Docker images can be custom-built using Dockerfiles. This allows for creating environments tailored to your application needs.

# ### Dockerfile Basics
A Dockerfile is a script with a list of instructions to create a Docker image, such as installing software or copying files.

# ```dockerfile
# FROM ubuntu
# RUN apt-get update
# RUN apt-get install -y python3
# ```

# ### Build Commands
Build your image using `docker build`, optionally tagging it with a name and version.

# ```bash
# #
docker build -t my_image .       # Build image from Dockerfile
docker build -t my_image:v1 .    # Tag image with version
# ```

# ### Copying Files
Use the `COPY` instruction in Dockerfile to include local files or folders into the image.

# ```dockerfile
# COPY ./source.py /app/
# COPY ./folder/ /app/
# ```

# ### Downloading Files
Sometimes it’s better to download files during the image build using `curl` or `wget` instead of copying them.

# ```dockerfile
# RUN curl -O http://example.com/file.zip && unzip file.zip && rm file.zip
# ```

# ### Setting Start Commands
The `CMD` instruction defines the default command to execute when a container is started from the image.

# ```dockerfile
# CMD ["python3", "my_script.py"]
# ```

# Override:

# ```bash
# #
docker run my_image python3 another_script.py  # Override default command
# ```

# ---

# ## Section Four: Security and Optimization
### Overview
Secure Docker practices help reduce vulnerabilities. This section also introduces how to optimize image builds with Dockerfile features.

# ### Working Directory and Users
Use `WORKDIR` to avoid long paths and `USER` to drop unnecessary root privileges during container execution.

# ```dockerfile
# WORKDIR /app
# USER repl
# ```

# ### Variables
ARG is for build-time variables, while ENV sets runtime environment variables. They help make Dockerfiles more flexible and reusable.

# - Build-time: `ARG`
# - Run-time: `ENV`

# ```dockerfile
# ARG version=3.9
# ENV MODE=production
# ```

# Override at build/run:

# ```bash
# #
docker build --build-arg version=3.8 .  # Set ARG value during build
docker run --env MODE=debug my_image   # Set ENV variable at runtime
# ```

# ### Caching and Layers
Each Dockerfile instruction creates a layer. Docker caches unchanged layers, making future builds faster and more efficient.

# - Each `RUN`, `COPY`, etc., creates a layer.
# - Docker caches unchanged layers for faster rebuilds.

# ### Multi-Stage Builds
Multi-stage builds reduce final image size by separating the build and runtime environments in different stages.

# ```dockerfile
# FROM node:14 AS builder
# WORKDIR /app
# COPY . .
# RUN npm run build

# FROM nginx:alpine
# COPY --from=builder /app/build /usr/share/nginx/html
# ```

# ### Security Best Practices
Follow practices such as using official base images, installing only necessary software, avoiding root users, and keeping images updated.

# - Use official base images.
# - Minimize installed software.
# - Avoid running as root.
# - Keep images up-to-date.

# ---

# ## Summary Cheat Sheet

# | Task                              | Command                                           |
# |-----------------------------------|---------------------------------------------------|
# | Run container                     | `docker run <image>`                              |
# | Run interactive container         | `docker run -it <image>`                          |
# | Run in background (detached)      | `docker run -d <image>`                           |
# | List running containers           | `docker ps`                                       |
# | Stop a container                  | `docker stop <container-id>`                      |
# | View logs                         | `docker logs <container-id>`                      |
# | Remove stopped container          | `docker container rm <container-id>`              |
# | Pull image                        | `docker pull <image>`                             |
# | Pull specific image version       | `docker pull <image>:<version>`                   |
# | Build image                       | `docker build -t <name> .`                        |
# | Tag image                         | `docker tag <old> <new>`                          |
# | Push image                        | `docker push <image>`                             |
# | Save image to file                | `docker save -o file.tar <image>`                 |
# | Load image from file              | `docker load -i file.tar`                         |
# | Prune unused containers/images    | `docker container prune` / `docker image prune -a`|

# ---

# ## For more information:

# - Docker Documentation: https://docs.docker.com/
# - Dockerfile Best Practices: https://docs.docker.com/develop/develop-images/dockerfile_best-practices/