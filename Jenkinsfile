pipeline {
    agent any
    
    tools {
        maven "M3"
        jdk "JDK17"
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

                    
        stage('SSH publish') {
            steps {
                sshPublisher(publishers: [sshPublisherDesc(configName: 'target', 
                transfers: [sshTransfer(cleanRemote: false, excludes: '', 
                execCommand: '''fuser -k 8080/tcp
                export BUILD_ID=Petclinic-Pipeline
                nohup java -jar /home/ubuntu/deploy/spring-petclinic-2.7.3.jar >> nohup.out 2>&1 &''', 
                execTimeout: 120000, 
                flatten: false, 
                makeEmptyDirs: false, 
                noDefaultExcludes: false, 
                patternSeparator: '[, ]+', 
                remoteDirectory: '', 
                remoteDirectorySDF: false, 
                removePrefix: 'target', 
                sourceFiles: 'target/*.jar')], 
                usePromotionTimestamp: false, 
                useWorkspaceInPromotion: false, verbose: false)])
            }
        }
    }
}
