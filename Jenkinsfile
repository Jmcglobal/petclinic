pipeline {
    agent any
    
    tools{
        // jdk 'jdk-11'
        maven 'maven3'
    }
    
    stages {
        stage('Git Clone') {
            steps {
                git branch: 'main', url: 'https://github.com/Jmcglobal/petclinic.git'
            }
        }
        
        stage('mvn compile command') {
            steps {
                sh "mvn compile"
            }
        }
        
        stage('mvn test command') {
            steps {
                sh "mvn test"
            }
        }

        stage('Sonarqube Analysis') {
            steps {
                withSonarQubeEnv('sonarqube') {
                    sh '''mvn clean verify sonar:sonar \
                    -Dsonar.projectName=Petclinic \
                    -Dsonar.projectKey=Petclinic'''
                }
            }
        }

    // OR
        // stage('Sonarqube Analysis') {
        //     steps {
        //       sh '''mvn clean verify sonar:sonar \
        //         -Dsonar.projectKey=${SONAR_PROJECT_KEY} \
        //         -Dsonar.host.url=${SONAR_URL} \
        //         -Dsonar.login=${SONAR_TOKEN}'''
        //     }
        // }
        
        stage('mvn clean package') {
            steps {
                sh "mvn clean package"
            }
        }

        stage('Dependency-Check') {
            steps {
                dependencyCheck additionalArguments: '--scan target/', odcInstallation: 'owasp'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        
        
        stage('deploy to tomcat webapps folder') {
            steps {
                sh "cp target/petclinic.war /opt/apache-tomcat-9.0.65/webapps/"
            }
        }
    }
}
