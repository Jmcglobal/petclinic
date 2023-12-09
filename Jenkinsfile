pipeline {
    agent any
    
    tools{
        // jdk 'jdk-11'
        maven 'maven3'
    }

    environment {
        SONAR_TOKEN = credentials('sonar')
        SONAR_URL = 'http://3.238.217.125:9000'
        SONAR_PROJECT_KEY ='sonarqube'
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
        // eploy Artifacts to Nexus repository
        stage('Nexus Repository Deploy') {
            steps {
                configFileProvider([configFile(fileId: 'e48fe939-a536-45fc-bcec-9a11e7e76ecb', variable: 'mavensettings')]) {
                sh "mvn -s $mavensettings clean deploy -DskipTests=true"
                }
            }
        }

        stage('DOCKER BUILDS AND PUSH') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'docker', toolName: 'docker') {
                        sh "docker build -t jmcglobal/petclinic ."
                        sh " docker push jmcglobal/petclinic:latest"
                    }
                }
            }
        }

        stage('DOCKER RUN COMMAND') {
            steps {
                sh "docker run -d --rm --name petclinic -p8082:8082 jmcglobal/petclinic"
            }
        }
        // Deploy Or Copy to Tomcat server path
        stage('deploy to tomcat webapps folder') {
            steps {
                sh "cp target/petclinic.war /opt/apache-tomcat-9.0.65/webapps/"
            }
        }
    }
}
