pipeline {
    agent any
    tools { 
        maven '/opt/maven/apache-maven-3.6.3' 
        jdk 'jdk8' 
    }
    stages {
        stage ('Initialize') {
            steps {
                // Path where installed maven ( mvn )
                sh '''
                    echo "PATH = ${PATH}"
                    echo "M2_HOME = ${M2_HOME}"
                ''' 
            }
        }

        
        stage ('Build') {
            steps {
                sh 'mvn -Dmaven.test.failure.ignore=true install' 
            }
        }
        
        
        stage('Test') { 
            steps {
                sh 'mvn test' 
            }
            // If the maven build succeeded, archive the JUnit test reports for display in the Jenkins web UI.
            post {
                success {
                    junit 'target/surefire-reports/*.xml' 
                }
            }
        }
    }
}
