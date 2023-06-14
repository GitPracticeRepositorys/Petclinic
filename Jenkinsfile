pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                script {
                    def gitPath = "/usr/bin/git"  // Replace with the correct path to the Git executable on your system
                    
                    // Run the Git commands using the specified executable path
                    sh "${gitPath} rev-parse --resolve-git-dir /var/lib/jenkins/workspace/petclinic/.git"
                    sh "${gitPath} config remote.origin.url https://github.com/GitPracticeRepositorys/Petclinic.git"
                    sh "${gitPath} fetch"
                    // ...
                    // Other Git commands as needed
                }
            }
        }
        
        stage("OWASP Dependency Check") {
            steps {
                dependencyCheck additionalArguments: '--scan ./ --format HTML ', odcInstallation: 'DP'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        
        stage("Build") {
            steps {
                sh "mvn clean install"
            }
        }
        
        stage("Docker Build & Push") {
            steps {
                script {
                    withDockerRegistry(credentialsId: '58be877c-9294-410e-98ee-6a959d73b352', toolName: 'docker') {
                        sh "docker build -t image1 ."
                        sh "docker tag image1 shivakrishna99/pet-clinic123:latest"
                        sh "docker push shivakrishna99/pet-clinic123:latest"
                    }
                }
            }
        }
        
        stage("TRIVY") {
            steps {
                sh "trivy image shivakrishna99/pet-clinic123:latest"
            }
        }
        
        stage("Deploy To Tomcat") {
            steps {
                sh "cp /var/lib/jenkins/workspace/CI-CD/target/petclinic.war /opt/apache-tomcat-9.0.65/webapps/"
            }
        }
    }
}
