# Maven + Jenkins + Nexus + Docker Deployment - Setup Steps

## 1️⃣. Create Ubuntu Server
- Instance type: t2.medium
- Disk size: 30 GB

---

## 2️⃣. Install Jenkins
- Follow official guide:  
  https://www.jenkins.io/doc/book/installing/linux/

---

## 3️⃣. Install Maven 3.8.7
```bash
apt install maven -y
```

---

## 4️⃣. Install Nexus Repository as Docker Container
```bash
apt install docker.io -y
docker pull sonatype/nexus3
docker volume create nexus-data
docker run -d -p 8081:8081 --name nexus -v nexus-data:/nexus-data sonatype/nexus3
```

---

## 5️⃣. Fetch Nexus Admin Password
```bash
docker ps
docker exec -it nexus /bin/bash
cat /nexus-data/admin.password
```

---

## 6️⃣. Install Required Jenkins Plugins
1. Pipeline Maven Integration  
2. Nexus Artifact Uploader

---

## 7️⃣. Configure Maven Installation in Jenkins
- Go to:  
  Manage Jenkins → Global Tool Configuration → Maven installation  
  - Name: `M3`  
  - MAVEN_HOME: `/usr/share/maven`

---

## 8️⃣. Add Global Maven Settings Config
- Manage Jenkins → Managed Files → Add new config (Global-Setting) → Content:
```xml
<servers>
    <server>
      <id>maven-releases</id>
      <username>admin</username>
      <password>admin123</password>
    </server>
    <server>
      <id>maven-snapshots</id>
      <username>admin</username>
      <password>admin123</password>
    </server>
</servers>
```

---

## 9️⃣. Configure Maven Settings File for Jenkins User
```bash
ls -l /var/lib/jenkins/.m2/
mkdir -p /var/lib/jenkins/.m2
vim /var/lib/jenkins/.m2/settings.xml
```

Add content:
```xml
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0
                              https://maven.apache.org/xsd/settings-1.0.0.xsd">

  <servers>
    <server>
      <id>maven-releases</id>
      <username>admin</username>
      <password>admin123</password>
    </server>

    <server>
      <id>maven-snapshots</id>
      <username>admin</username>
      <password>admin123</password>
    </server>
  </servers>
</settings>
```

---

## 🔟. Update `pom.xml`
- Set Nexus server IP (example):  
  `http://52.66.206.151:8081/repository/maven-releases/`

---

## 1️⃣1️⃣. Set Jenkins User Permissions
```bash
vim /etc/sudoers
```

Add line:
```
jenkins ALL=(ALL) NOPASSWD: /bin/systemctl restart nginx, /bin/rm, /bin/cp
```

Or run:
```bash
sudo chown -R jenkins:jenkins /var/www/html
```

---

## 1️⃣2️⃣. Final Commands (Manual Testing)

### Build locally
```bash
mvn clean package
```

### Upload JAR manually to Nexus
```bash
mvn deploy:deploy-file     -Durl=http://<nexus-ip>:8081/repository/maven-releases/     -DrepositoryId=nexus-releases     -DgroupId=com.example     -DartifactId=my-app     -Dversion=1.0-SNAPSHOT     -Dpackaging=jar     -Dfile=target/my-app-1.0-SNAPSHOT.jar
```

### Download artifact from Nexus
```bash
curl -u admin:admin123 -O http://<nexus-ip>:8081/repository/maven-releases/com/example/my-app/1.0-SNAPSHOT/my-app-1.0-SNAPSHOT.jar
```

### Deploy to Nginx Web Server
```bash
sudo rm -rf /var/www/html/*
sudo cp target/my-app-1.0-SNAPSHOT.jar /var/www/html/
sudo systemctl restart nginx
```

---

## ✅ Done
Your Maven app is now built, uploaded to Nexus, and deployed using Docker & Jenkins.