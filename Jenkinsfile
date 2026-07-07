pipeline {
    agent any
    
    tools{
        jdk 'JAVA_HOME'
        maven 'MAVEN_HOME'
    }
    stages {
        stage('clone spring boot repo') {
            steps {
                git branch: 'main', url: 'https://github.com/shashankJ17/spring-framework-petclinic.git'
            }
        }
        stage('Compile the spring petclinic project') {
            steps {
                sh 'mvn compile'
                
            }
        }
        stage('test the spring petclinic project') {
            steps {
                sh 'mvn test'
                
            }
        }
        stage('Analyzing source code via Sonarqube') {
            steps {
                withSonarQubeEnv('SONAR_HOME') {
                    sh 'mvn verify sonar:sonar -Dsonar.projectKey=spring-petclinic -Dsonar.projectName=spring-petclinic'
                }
            }
        }
        stage('Generate package for the spring petclinic project') {
            steps {
                sh 'mvn package'
            }
        }
        stage('Deploy build into the Nexus') {
            steps {
                withMaven(globalMavenSettingsConfig: 'Nexus-repository', jdk: 'JAVA_HOME', maven: 'MAVEN_HOME', traceability: true) {
                    sh 'mvn deploy'
                }
            }
        }
         stage('Hosting the spring petclinic via tomcat') {
            steps {
                sshagent(['tomcat']) {
                    sh 'scp -o StrictHostKeyChecking=no target/petclinic.war ubuntu@13.201.172.212:/home/ubuntu/apache-tomcat-10.1.56/webapps'
                }
            }
        }
    }
    post {
    success {
        mail(
            to: 'shashankjayram2004@gmail.com',
            cc: 'leelavathijayram298@gmail.com',
            subject: 'Build Successful',
            body: 'The Jenkins pipeline completed successfully.'
        )
    }

    failure {
        mail(
            to: 'shashankjayram2004@gmail.com',
            cc: 'leelavathijayram298@gmail.com',
            subject: 'Build Failed',
            body: 'The Jenkins pipeline failed. Please check the Jenkins console output.'
        )
    }
}
}
