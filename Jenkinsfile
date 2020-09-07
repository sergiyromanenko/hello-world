node {
    agent any
    tools {
        maven '/opt/maven/apache-maven-3.6.3'
    }

   stage ('Initialize') {
       steps {
           sh '''
               echo "PATH = ${PATH}"
               echo "M2_HOME = ${M2_HOME}"
               sh 'java -version'
           '''
       }
   }

   def mvnHome = tool 'M2_HOME'

   stage('getscm') { // for display purposes
      // Get some code from a GitHub repository
      git 'https://github.com/sergiyromanenko/hello-world.git'
      mvnHome = tool 'maven'
   }

   stage('Build') {
      // Run the maven build
      steps {
          sh '${mvnHome}/bin/mvn -Dmaven.test.failure.ignore=true install'
      }
      post {
          success {
              junit 'target/surefire-reports/**/*.xml'
          }
      }
   }

   stage('artifact') {
      archive 'target/*.war'
   }

   stage ('deploy'){
   echo 'deployment started'
       cp webapp.war /opt/apache-tomcat-8.5.57/webapps/
   }
}

