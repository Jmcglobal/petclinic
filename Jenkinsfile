pipeline {
    agent any
    
    tools{
        // jdk 'jdk-11'
        maven 'maven3'
    }
    

    stages {
        stage('Git Clone') {
            steps {
                git branch: 'feature-1', url: 'https://github.com/Jmcglobal/petclinic.git'
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
        
        stage('mvn clean package') {
            steps {
                sh "mvn clean package"
            }
        }
        
        stage('deploy to tomcat webapps folder') {
            steps {
                sh "cp target/petclinic.war /opt/apache-tomcat-9.0.65/webapps/"
            }
        }
    }
}
