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
            // This command generates a JUnit XML report, which is saved to the target/surefire-reports directory
            // (within the /var/jenkins_home/workspace/simple-java-maven-app directory in the Jenkins container).
        //    post {
       //         success {
        //            junit '/var/jenkins/workspace/Deploy_Pipeline_On_Tomcat_VM/webapp/target/surefire-reports/*.xml' 
        //        }
       //     }
        }
        
        
        
    }
}
