pipeline {
    agent none

    stages {
        stage('Run on JDK11 Node') {
            agent {
                label 'jdk11'
            }

            tools {
                jdk 'jdk11'
                maven 'MAVEN_3.6.3'
            }

            environment {
                SCANNER_HOME = tool 'sonar'
            }

            steps {
                // Your steps go here
                git branch: 'main', changelog: false, poll: false, url: 'https://github.com/GitPracticeRepositorys/Petclinic.git'
            }
        }
        
        stage("Compile") {
            agent {
                label 'jdk11'
            }
            
            steps {
                sh "mvn clean compile"
            }
        }
        
        stage("Test Cases") {
            agent {
                label 'jdk11'
            }
            
            steps {
                sh "mvn test"
            }
        }
        
        stage("Sonarqube Analysis") {
            agent {
                label 'jdk11'
            }
            
            environment {
                SCANNER_HOME = tool 'sonar'
            }
            
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Petclinic \
                        -Dsonar.java.binaries=. \
                        -Dsonar.projectKey=Petclinic '''
                }
            }
        }
        
        stage("OWASP Dependency Check") {
            agent {
                label 'jdk11'
            }
            
            steps {
                dependencyCheck additionalArguments: '--scan ./ --format HTML', odcInstallation: 'DP'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        
        stage("Build") {
            agent {
                label 'jdk11'
            }
            
            steps {
                sh "mvn clean install"
            }
        }
        
        stage("Docker Build & Push") {
            agent {
                label 'jdk11'
            }
            
            steps {
                script {
                    sh "docker build -t image1 ."
                    sh "docker tag image1 shivakrishna99/pet-clinic123:latest"
                    sh "docker push shivakrishna99/pet-clinic123:latest"
                }
            }
        }
        
        stage("TRIVY") {
            agent {
                label 'jdk11'
            }
            
            steps {
                sh "trivy image shivakrishna99/pet-clinic123:latest"
            }
        }
        
        stage("Deploy To Tomcat") {
            agent {
                label 'jdk11'
            }
            
            steps {
                sh "cp /var/lib/jenkins/workspace/CI-CD/target/petclinic.war /opt/apache-tomcat-9.0.65/webapps/"
            }
        }
    }
}
