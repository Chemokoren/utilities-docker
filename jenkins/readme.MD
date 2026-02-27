---

### 🧭 Instructions

#### 1. Start Jenkins

```bash
docker compose up -d
```

#### 2. View logs

```bash
docker compose logs -f jenkins
```

Wait until you see something like:

```
Jenkins is fully up and running
```

#### 3. Access Jenkins

Open your browser and go to:

```
http://localhost:8080
```

#### 4. Get initial admin password

```bash
docker exec -it jenkins cat /var/jenkins_home/secrets/initialAdminPassword
```

Use that password to unlock Jenkins on first login.

---

### 🧰 Optional Enhancements

* **Persist Jenkins home to a host path** (instead of a Docker volume):

  ```yaml
  volumes:
    - ./jenkins_data:/var/jenkins_home
  ```

* **Add proxy support**:

  ```yaml
  environment:
    - JENKINS_OPTS=--prefix=/jenkins
  ```

* **Add plugin installation automation** using environment variables:

  ```yaml
  environment:
    - JENKINS_UC=https://updates.jenkins.io
    - JENKINS_INSTALL_RUN_SETUP_WIZARD=false
  ```

---

Would you like me to extend this setup with **a reverse proxy (Nginx + HTTPS using Let’s Encrypt)** so it’s ready for production deployment?
