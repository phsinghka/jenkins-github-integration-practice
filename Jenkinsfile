pipeline {
    agent any

    environment {
        AWS_ACCESS_KEY_ID = credentials('aws_access_key')
        AWS_SECRET_ACCESS_KEY = credentials('aws_secret_key')
    }

    stages {
        stage('Checkout Code') {
            steps {
                git url: 'https://github.com/phsinghka/jenkins-github-integration-practice.git', branch: 'java-app'
            }
        }

        stage('Build with Maven') {
            steps {
                sh 'mvn clean install'
            }
        }

                stage('CODE ANALYSIS with SONARQUBE') {
          
		  environment {
             scannerHome = tool 'sonarscanner4'
          }

          steps {
            withSonarQubeEnv('sonar-pro') {
               sh '''${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=java-app \
                   -Dsonar.projectName=java_app \
                   -Dsonar.projectVersion=1.0 \
                   -Dsonar.sources=src/ \
                   -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/ \
                   -Dsonar.junit.reportsPath=target/surefire-reports/ \
                   -Dsonar.jacoco.reportsPath=target/jacoco.exec \
                   -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml'''
            }

            timeout(time: 10, unit: 'MINUTES') {
               waitForQualityGate abortPipeline: true
            }
          }
        }

        stage('Upload to Nexus') {
            steps {
                script {
                    // Uploading to Nexus Repository
                    sh '''
                    mvn deploy:deploy-file \
                        -Durl=http://192.168.10.100:8081/repository/maven-releases/ \
                        -DrepositoryId=nexus \
                        -DpomFile=pom.xml \
                        -Dfile=target/myapp.jar
                    '''
                }
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                script {
                    // Deploying to a remote Tomcat server on an AWS EC2 instance
                    sh '''
                    scp target/myapp.war ec2-user@18.217.45.201:/opt/tomcat/webapps/
                    '''
                }
            }
        }


    }
}
