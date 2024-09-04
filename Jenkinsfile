pipeline {
    agent any
    
    tools {
        maven "M3"
        jdk "JDK17"
    }
    environment {
        // jenkins에 등록해 놓은 docker hub credentials 이름
        DOCKERHUB_CREDENTIALS = credentials('DockerHub')
    }
    
    stages {
        stage('Git Clone') {
            steps {
                git url: 'https://github.com/sjh4616/spring-petclinic.git',
                branch: 'efficient-webjars'
            }
        }
        stage('Build') {
            steps {
                sh 'mvn -Dmaven.test.failure.ignore=true clean package'
            }
        }
        // Docker Images 생성
        stage('Docker Image Build') {
            steps {
                dir("${env.WORKSPACE}") {
                    sh """ 
                    docker build -t hsjo803/spring-petclinic:$BUILD_NUMBER .
                    docker tag hsjo803/spring-petclinic:$BUILD_NUMBER hsjo803/spring-petclinic:latest
                    """
                }
            }
        }

        // Docker Login
        stage('Docker Login') {
            steps {
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            }
        }

        // Docker Image Push
        stage('Docker Image Push') {
            steps {
                sh 'docker push hsjo803/spring-petclinic:latest'
            }
        }
        stage('SSH publish') {
            steps {
                sshPublisher(publishers: [sshPublisherDesc(configName: 'target', 
                transfers: [sshTransfer(cleanRemote: false, excludes: '', 
                execCommand: '''
                docker rm -f $(docker ps -aq)
                docker rmi $(docker images -q)
                docker pull hsjo803/spring-petclinic:latest
                docker run -d -p 80:8080 --name spring-petclinic hsjo803/spring-petclinic:latest
                ''',
                execTimeout: 120000, 
                flatten: false, 
                makeEmptyDirs: false, 
                noDefaultExcludes: false, 
                patternSeparator: '[, ]+', 
                remoteDirectory: '', 
                remoteDirectorySDF: false, 
                removePrefix: 'target', 
                usePromotionTimestamp: false, 
                useWorkspaceInPromotion: false, verbose: false)])])
                
            }
        }
    }
}
