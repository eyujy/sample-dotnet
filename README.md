# DevOps Demo Document

## Demo SETUP

2x ubuntu 20.04 VM

---

## Jenkins

### Installation

[https://www.jenkins.io/doc/book/installing/](https://www.jenkins.io/doc/book/installing/)

**Linux** [https://www.jenkins.io/doc/book/installing/linux/#debianubuntu](https://www.jenkins.io/doc/book/installing/linux/#debianubuntu)

```bash
## Requires java 
sudo apt install openjdk-11-jdk
java -version

curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins
```

**macOS** [https://www.jenkins.io/doc/book/installing/macos/](https://www.jenkins.io/doc/book/installing/macos/)

```yaml
brew install jenkins-lts
brew services start jenkins-lts
brew services restart jenkins-lts
```

## Manual Steps

### 1 ************************************Sample Application************************************

- .net 7

[https://github.com/eyujy/sample-dotnet.git](https://github.com/eyujy/sample-dotnet.git) 

### 2 Sonarqube

[https://www.sonarsource.com/products/sonarqube/](https://www.sonarsource.com/products/sonarqube/)

**Installation**

[https://docs.sonarqube.org/latest/try-out-sonarqube/](https://docs.sonarqube.org/latest/try-out-sonarqube/)

```bash
docker run -d --name sonarqube -e SONAR_ES_BOOTSTRAP_CHECKS_DISABLE=true -p 9000:9000 sonarqube:9.9.0-community
```

### 3 Docker

- Install docker
- Dockerfile
- Docker image build, tag, push
- Docker run
    - [https://docs.docker.com/engine/reference/commandline/run/#name](https://docs.docker.com/engine/reference/commandline/run/#name)

```bash
git clone https://github.com/eyujy/sample-dotnet.git

ls

dotnet sonarscanner begin /k:"cop-demo-0" /d:sonar.host.url="http://127.0.0.1:32434"  /d:sonar.token="sqp_913810ddb78e20caee74f28df3a16e776bb3486e"
dotnet build
dotnet sonarscanner end /d:sonar.token="sqp_913810ddb78e20caee74f28df3a16e776bb3486e"
            
docker build -t eyujy/sample-dotnet:demo-0 .
docker login -u eyujy -p <TOKEN>
docker push eyujy/sample-dotnet:demo-0
            
docker run -d --name cop-demo-0 -p 1234:80 eyujy/sample-dotnet:demo-0 
```

---

## Jenkins -cont’d

### Configuration

- Adding node to Jenkins

### Scripts

A `Jenkinsfile` can be written using two types of syntax - Declarative and Scripted.

Declarative and Scripted Pipelines are constructed fundamentally differently. Declarative Pipeline is a more recent feature of Jenkins Pipeline which:

- provides richer syntactical features over Scripted Pipeline syntax, and
- is designed to make writing and reading Pipeline code easier.

Many of the individual syntactical components (or "steps") written into a `Jenkinsfile`, however, are common to both Declarative and Scripted Pipeline. Read more about how these two types of syntax differ in [Pipeline concepts](https://www.jenkins.io/doc/book/pipeline/#pipeline-concepts) and [Pipeline syntax overview](https://www.jenkins.io/doc/book/pipeline/#pipeline-syntax-overview) below.

Declarative

```jsx
pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo 'Building..'
            }
        }
        stage('Test') {
            steps {
                echo 'Testing..'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying....'
            }
        }
    }
}
```

✅ Able to *restart from stage* 

✅ Validates Jenkinsfile script syntax before execution

✅ Able to *skip stage on condition* using when conditions

✅ Options Block

Scripted

```jsx
node {
    stage('Build') {
        echo 'Building....'
    }
    stage('Test') {
        echo 'Testing....'
    }
    stage('Deploy') {
        echo 'Deploying....'
    }
}
```

❌ ~~Restart from stage~~

❌ Runs up till current stage before pipeline syntax error

❌  S~~kip stage on condition~~

❌ Options need to be nested

**SONARQUBE**

URL: 172.20.105.97:9000

username: admin

password: P@ssw0rd

| VM | sonarqube project |
| --- | --- |
| Jia Yi | cop-demo-0 |
| 1 | cop-demo-1 |
| 2 | cop-demo-2 |
| 3 | cop-demo-3 |
| 4 | cop-demo-4 |
| 5 | cop-demo-5 |

<aside>
💡 Create sonarqube project and note down commands

</aside>

| VM | docker image tag | docker container port |
| --- | --- | --- |
| Jia Yi | demo-0 | 1234 |
| 1 | demo-1 | 1235 |
| 2 | demo-2 | 1236 |
| 3 | demo-3 | 1237 |
| 4 | demo-4 | 1238 |
| 5 | demo-5 | 1239 |

<aside>
💡 Note down docker related commands (build, push, run)

</aside>

**JENKINS**

URL: 172.20.105.98:8090

username: admin

password: P@ssw0rd

<aside>
💡 Create jenkins pipeline

</aside>

| VM | pipeline name |
| --- | --- |
| Jia Yi | cop-demo-0 |
| 1 | cop-demo-1 |
| 2 | cop-demo-2 |
| 3 | cop-demo-3 |
| 4 | cop-demo-4 |
| 5 | cop-demo-5 |

<aside>
💡 Run sample declarative pipeline

</aside>

<aside>
💡 Map manual steps into pipeline
Run pipeline and access container at 172.20.105.97:<portnumber>/weatherforecast

</aside>

### Parameterisation/ Environment Variables

[https://wiki.jenkins.io/display/JENKINS/Parameterized+Build](https://wiki.jenkins.io/display/JENKINS/Parameterized+Build)

Within Jenkinsfile

```bash
environment {
	SERVICE_NAME = 'dashboard-dotnet'
	LANGUAGE = 'dotnet'
	TYPE = 'backend-api' // options: frontend, backend-api, backend-others
	PROJECT_NAME = 'tr-pqm'
	HARBOR_CREDENTIALS = credentials('harbor-tr-pqm')
	
	SONAR_URL = 'http://tools.mf.platform/sonar/'
	SONAR_PROJECT_NAME = 'tr-pqm2-dashboard-dotnet'
	SONAR_LOGIN = 'sqp_8bcf5008da2fd1e8973add9d3573d174856a4d2c' // the sonar.login hash
}
```

[https://www.jenkins.io/doc/book/pipeline/jenkinsfile/#using-environment-variables](https://www.jenkins.io/doc/book/pipeline/jenkinsfile/#using-environment-variables)

```jsx
pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo 'Building..'
            }
        }
        stage('Test') {
            steps {
                echo 'Testing..'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying....'
            }
        }
    }
}
```

```bash
pipeline {
    agent any
    stages {
        stage('Environment Variables') {
            steps {
                sh 'printenv'
            }
        }
        stage('Github - Clone source code ') {
            steps {
                git branch: 'main', url: 'https://github.com/eyujy/sample-dotnet.git'
            }
        }
        stage('SAST - Sonarqube') {
            steps {
                sh 'dotnet sonarscanner begin /k:"cop-demo-0" /d:sonar.host.url="http://172.20.105.97:9000"  /d:sonar.login="sqp_e2dc8d7819a11921785b0791ec8ab528dabc26e0"'
                sh 'dotnet build'
                sh 'dotnet sonarscanner end /d:sonar.login="sqp_e2dc8d7819a11921785b0791ec8ab528dabc26e0"'

            }
        }
        stage('Docker Build & Push') {
            steps {
                sh 'docker build -t eyujy/sample-dotnet:demo-0 .'
                sh 'docker login -u eyujy -p dckr_pat_dhYislPjnvEuL8e1gEdbYH2wCtg'
                sh 'docker push eyujy/sample-dotnet:demo-0'
            }
        }
        stage('Run as Docker Container'){
            steps {
                sh 'docker run -d --name cop-demo-0 -p 1234:80 eyujy/sample-dotnet:demo-0'
            }
        }
    }
}
```

```bash
pipeline {
    agent any
		environment {
        DOCKER_CREDENTIALS = credentials('docker-eyujy')
        SERVICE_NAME = 'dotnet-sample'

    }
    stages {
        stage('Environment Variables') {
            steps {
                sh 'printenv'
            }
        }
        stage('Github - Clone source code ') {
            steps {
                git branch: 'main', credentialsId: 'github-eyujy', url: 'https://github.com/eyujy/sample-dotnet.git'
                sh 'ls'
            }
        }
        stage('SAST - Sonarqube') {
            steps {
                sh 'dotnet sonarscanner begin /k:"cop-demo-0" /d:sonar.host.url="http://172.20.105.97:9000"  /d:sonar.login="sqp_e2dc8d7819a11921785b0791ec8ab528dabc26e0"'
                sh 'dotnet build'
                sh 'dotnet sonarscanner end /d:sonar.login="sqp_e2dc8d7819a11921785b0791ec8ab528dabc26e0"'

            }
        }
        stage('Docker Build & Push') {
            steps {
                sh 'docker build -t $DOCKER_CREDENTIALS_USR/$SERVICE_NAME:$BUILD_NUMBER .'
	              sh 'docker login -u $DOCKER_CREDENTIALS_USR -p $DOCKER_CREDENTIALS_PSW'
	              sh 'docker push $DOCKER_CREDENTIALS_USR/$SERVICE_NAME:$BUILD_NUMBER'
	              sh 'docker rmi $DOCKER_CREDENTIALS_USR/$SERVICE_NAME:$BUILD_NUMBER'
            }
        }
        stage('Run as Docker Container'){
            steps {
                sh 'docker run -d --name $SERVICE_NAME  -p 12340:80 $DOCKER_CREDENTIALS_USR/$SERVICE_NAME:$BUILD_NUMBER'
            }
        }
    }
}
```