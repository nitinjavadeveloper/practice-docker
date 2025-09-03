# Docker Interview Questions and Answers for Experienced

This document covers core Docker concepts, scenario-based questions, command-line practices, and real-world DevOps tasks for experienced engineers.

---

## 🔹 Core Docker Concepts

1. **What is Docker? Why is it useful?**  
   Docker is a containerization platform that packages applications with their dependencies into lightweight, portable containers. It helps in consistent deployment across environments and faster delivery.

2. **What is the difference between Docker Image and Docker Container?**  
   | Docker Image      | Docker Container         |
   |-------------------|-------------------------|
   | Blueprint/template| Running instance of image|
   | Read-only         | Writable layer added     |
   | Can’t run directly| Executes as container    |

3. **What is Dockerfile?**  
   A text file with instructions to build a Docker image.  
   Common instructions: `FROM`, `COPY`, `RUN`, `CMD`, `ENTRYPOINT`, `ENV`, `EXPOSE`, etc.

4. **What is the difference between CMD and ENTRYPOINT in Dockerfile?**  
   - CMD: Default command, overridden if user passes arguments.  
   - ENTRYPOINT: Fixed entry, treats CMD as arguments.  
   Use both together for flexible containers.

5. **What is the purpose of .dockerignore file?**  
   Excludes files/folders from the build context (like `.git`, `node_modules`), reducing image size and improving performance.

6. **What is the difference between COPY and ADD in Dockerfile?**  
   - COPY: Simple file/folder copy.  
   - ADD: Adds extra features (extracts tar, supports URLs).  
   Best practice: Use COPY unless ADD features are needed.

7. **What is a multi-stage build in Docker?**  
   Used to create smaller final images by separating build and runtime stages.

   ```dockerfile
   FROM maven as builder
   WORKDIR /app
   COPY . .
   RUN mvn clean package

   FROM openjdk
   COPY --from=builder /app/target/app.jar .
   CMD ["java", "-jar", "app.jar"]
   ```

8. **What is the difference between bind mounts and volumes?**  
   | Bind Mount         | Volume                  |
   |--------------------|------------------------|
   | Maps to host FS    | Managed by Docker      |
   | No isolation       | More secure/portable   |
   | Used in dev        | Preferred in production|

9. **What is the use of docker-compose?**  
   Define multi-container apps using YAML.  
   Easy to set up full environments (web, db, cache, etc.)

   ```yaml
   services:
     web:
       build: .
       ports:
         - "8080:8080"
     db:
       image: postgres
   ```

10. **Difference between Docker Swarm and Kubernetes?**  
    | Docker Swarm      | Kubernetes              |
    |-------------------|------------------------|
    | Simpler, Docker-native | Industry standard  |
    | Limited features  | Rich ecosystem         |
    | Easier to set up  | More scalable/robust   |

11. **What are best practices to reduce Docker image size?**  
    - Use minimal base images (alpine)
    - Combine RUN commands
    - Clean up cache/artifacts
    - Multi-stage builds

12. **What happens when a container exits?**  
    If not restarted automatically, it goes into Exited state.  
    Use `docker ps -a` to see it.  
    Use `--restart` policy for automatic restart.

---

## 🔹 Essential Docker Commands & Troubleshooting

13. **How do you list all running containers?**  
    `docker ps`

14. **How do you list all containers (including stopped)?**  
    `docker ps -a`

15. **How do you view logs for a container?**  
    `docker logs <container>`

16. **How do you inspect a container’s configuration and environment?**  
    `docker inspect <container>`

17. **How do you execute a command inside a running container?**  
    `docker exec -it <container> /bin/bash`

18. **How do you build an image from a Dockerfile?**  
    `docker build -t my-app .`

19. **How do you run a container with port mapping?**  
    `docker run -p 8080:80 my-app`

20. **How do you stop and remove a container?**  
    `docker stop <container>`  
    `docker rm <container>`

21. **How do you remove unused images, containers, and volumes?**  
    `docker system prune -a`  
    `docker volume prune`  
    `docker image prune`

22. **How do you view resource usage (CPU/memory) for containers?**  
    `docker stats`

23. **How do you create and use a Docker volume?**  
    `docker volume create mydata`  
    `docker run -v mydata:/app/data my-app`

24. **How do you pass environment variables to a container?**  
    `docker run -e ENV_VAR=value my-app`

25. **How do you set restart policies for containers?**  
    `docker run --restart=on-failure:3 my-app`

26. **How do you network containers together?**  
    `docker network create mynet`  
    `docker run --network=mynet --name=db mysql`  
    `docker run --network=mynet --name=app my-app`

27. **How do you check which ports a container is exposing?**  
    `docker port <container>`

28. **How do you clean up dangling images?**  
    `docker image prune`

---

## 🔶 Scenario-Based Docker Interview Questions

29. **Your container crashes immediately after starting. How do you debug it?**  
    - `docker ps -a` → check status  
    - `docker logs <container>` → read logs  
    - `docker inspect <container>` → check config/env  
    - Use `CMD ["sh"]` in Dockerfile to debug manually.

30. **Your image build is slow. How do you optimize it?**  
    - Reduce context (`.dockerignore`)
    - Use efficient base images
    - Use build cache (order Dockerfile correctly)
    - Minimize RUN layers
    - Use multi-stage builds

31. **You want your container to restart automatically if it fails. How do you set this?**  
    Use `--restart` policy:  
    `docker run --restart=on-failure:3 my-app`  
    Options: `no` (default), `on-failure`, `always`, `unless-stopped`

32. **You need to pass secret credentials to a container. How do you do it securely?**  
    - Environment Variables (not secure if exposed)
    - Docker Secrets (only in Swarm)
    - Bind mount secret files
    - Use external secret managers (e.g., Vault, AWS Secrets Manager)

33. **Two containers need to communicate. How do you allow this?**  
    Use Docker network:  
    `docker network create mynet`  
    `docker run --network=mynet --name=db mysql`  
    `docker run --network=mynet --name=app my-app`  
    They can refer to each other by container name.

34. **You get “port already in use” error when running a container. How to fix it?**  
    - Check if host port is already in use: `lsof -i :<port>`
    - Run container with a different host port: `-p 8081:8080`
    - Stop conflicting container: `docker stop <container>`

35. **You need to persist data in a container. How do you do it?**  
    Use Volumes:  
    `docker volume create mydata`  
    `docker run -v mydata:/app/data my-app`  
    Or Bind Mount:  
    `docker run -v /host/path:/container/path my-app`

36. **How do you update a running container with a new version of code?**  
    - Rebuild the image with `docker build`
    - Stop and remove the old container
    - Run the new container with updated image
    - Or use `docker-compose up --build` to rebuild and restart automatically.

37. **How do you secure Docker containers?**  
    - Use trusted base images
    - Run as non-root user
    - Minimize attack surface (remove tools)
    - Use read-only filesystems
    - Scan images for vulnerabilities (e.g., Trivy, Clair)

38. **Your containerized app has a memory leak. How do you investigate and contain it?**  
    - Set memory limits: `docker run -m 512m --memory-swap 1g my-app`
    - Investigate with: `docker stats`
    - Inside container: `top`, `jcmd`, `jmap`, etc.
    - Enable logging/metrics inside container

39. **A container runs fine locally but fails in production. Why might this happen?**  
    - Different environment variables
    - Volume or network not available
    - OS-level differences (Ubuntu vs Alpine)
    - Missing dependencies in minimal image

40. **Your container runs but doesn’t respond on the expected port. How do you troubleshoot?**  
    - Check EXPOSE in Dockerfile
    - Ensure `-p` flag maps ports correctly
    - Inside container, run `netstat` or `ss` to verify listening port
    - Check firewall/host restrictions

41. **How do you clean up unused containers, images, and volumes?**  
    - `docker system prune -a`
    - `docker volume prune`
    - `docker image prune`
    - Be cautious — this can delete a lot!

---

## 🔹 Bonus: Real-Time DevOps + Docker Tasks

42. **Design a Docker-based CI/CD pipeline.**  
    - Build: Dockerfile + CI tool (Jenkins/GitLab)
    - Test: Run tests in container
    - Push: Push image to registry
    - Deploy: Use docker-compose or K8s (Helm) in next stage

43. **You’re asked to containerize a legacy monolith app. Steps?**  
    - Create Dockerfile
    - Resolve dependencies
    - Externalize config
    - Test in dev, QA
    - Create health checks
    - Build CI/CD around image

44. **How do you manage different environments (dev/test/prod) using Docker?**  
    - Use docker-compose with multiple override files
    - Use environment-specific `.env` files
    - Use Helm or Kustomize (if using Kubernetes)

45. **How do you scan Docker images for vulnerabilities?**  
    - Use tools like Trivy, Clair, Anchore, Snyk
    - Integrate scanning in CI/CD pipeline

46. **How do you optimize Docker for Java/Spring Boot apps?**  
    - Use multi-stage builds
    - Use slim base images (e.g., `openjdk:17-jdk-slim`)
    - Set JVM memory options
    - Use `.dockerignore` to exclude unnecessary files

47. **How do you troubleshoot slow container startup?**  
    - Check image size and layers
    - Check health checks and dependencies
    - Use logs and `docker events`

48. **How do you monitor running containers in production?**  
    - Use `docker stats`
    - Integrate with Prometheus, Grafana, ELK stack
    - Use container-specific logging drivers

49. **How do you handle rolling updates with Docker Compose?**  
    - Use `docker-compose up --build`
    - Use healthcheck and depends_on in compose file

50. **How do you automate Docker image builds and pushes in GitHub Actions or Jenkins?**  
    - Use official Docker actions/plugins
    - Store secrets securely
    - Tag images with commit SHA or version

---

## ✅ Want Practical Help?

Ask for:  
- Dockerfile best practices for Java apps  
- Multi-stage Dockerfile for Spring Boot  
- docker-compose for microservices  
- Image scan tools  
- Integration with Jenkins/GitHub Actions
