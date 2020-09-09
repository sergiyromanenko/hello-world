pipeline {
    agent {
        label 'node2-jenkins-slave'
    }
    tools { 
        maven '/opt/maven/apache-maven-3.6.3' 
        jdk 'jdk8' 
    }
    environment {
        NEXUS_VERSION = "nexus3"
        NEXUS_PROTOCOL = "http"
        NEXUS_URL = "nx.tehno.top"
        NEXUS_REPOSITORY = "Test-Maven-Snapshot"
        NEXUS_CREDENTIAL_ID = "nexus-user-credentials"
    }
    stages {
        stage ('Initialize') {
            steps {
                // Path where installed maven ( mvn )
                sh '''
                    echo "PATH = ${PATH}"
                    echo "M2_HOME = ${M2_HOME}"
                    cd ~
                    printenv
                '''
            }
        }

        
        stage ('Build') {
            steps {
                sh "mvn clean package -DskipTests=true"
         //       sh 'mvn -Dmaven.test.failure.ignore=true install'
                sh """
                  cd ~
                  printenv
                """
            }
        }
        
        
        stage('Test') { 
            steps {
                sh 'mvn test'
                sh """
                  cd ~
                  printenv
                """
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
        
              stage("Publish to Nexus Repository Manager") {
                  steps {
                      sh """
                           cd ~
                           printenv
                      """
                      script {
                          pom = readMavenPom file: "pom.xml";
                          echo "*** packaging: group: ${pom.groupId}, ${pom.packaging}, version ${pom.version}";
                          filesByGlob = findFiles(glob: "$WORKSPACE/*/target/*.${pom.packaging}");
                          echo "${filesByGlob[0].name} ${filesByGlob[0].path} ${filesByGlob[0].directory} ${filesByGlob[0].length} ${filesByGlob[0].lastModified}"
                          artifactPath = filesByGlob[0].path;
                          artifactExists = fileExists artifactPath;
                          if(artifactExists) {
                              echo "*** File: ${artifactPath}, group: ${pom.groupId}, packaging: ${pom.packaging}, version ${pom.version}";
                              nexusArtifactUploader(
                                  nexusVersion: NEXUS_VERSION,
                                  protocol: NEXUS_PROTOCOL,
                                  nexusUrl: NEXUS_URL,
                                  groupId: pom.groupId,
                                  version: pom.version,
                                  repository: NEXUS_REPOSITORY,
                                  credentialsId: NEXUS_CREDENTIAL_ID,
                                  artifacts: [
                                      [artifactId: pom.artifactId,
                                      classifier: 'debug',
                                      file: artifactPath,
                                      type: pom.packaging],
                                      [artifactId: pom.artifactId,
                                      classifier: 'debug',
                                      file: "pom.xml",
                                      type: "pom"]
                                  ]
                              );
                          } else {
                              error "*** File: ${artifactPath}, could not be found";
                          }
                      }
                  }
              }

        
    }
}
