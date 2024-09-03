pipeline {
    agent any
    
    tools {
        maven "M3"
        jdk "JDK17"
    }
    
    stages {
        stage('Git Clone') {
            steps {
                git url: 'https://github.com/hsjo803/spring-petclinic.git',
                branch: 'efficient-webjars'
            }
        }
        stage('Build') {
            steps {
                sh 'mvn -Dmaven.test.failure.ignore=true clean package'
            }
        }
        stage('SSH publish') {
            steps {
            }
        }    
    }
}
