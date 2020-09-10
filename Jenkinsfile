pipeline {
    agent {
        label 'node2-jenkins-slave'
    }
    tools { 
        maven '/opt/maven/apache-maven-3.6.3' 
        jdk 'jdk8' 
    }
    environment {
        // This can be nexus3 or nexus2
        NEXUS_VERSION = "nexus3"
        // This can be http or https
        NEXUS_PROTOCOL = "http"
        NEXUS_URL = "nx.tehno.top"
        // Nexus Repository where we will upload the artifact
        NEXUS_REPOSITORY = "Test-Maven-Snapshot"
        // Jenkins credential id to authenticate to Nexus OSS
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
          //      sh "mvn clean  -DskipTests=true"
                sh "mvn install -DskipTests=true -e -X"
                sh "mvn package -DskipTests=true -e -X"
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

                          // Read POM xml file using 'readMavenPom' step , this step 'readMavenPom' is included in: https://plugins.jenkins.io/pipeline-utility-steps
                          pom = readMavenPom file: "pom.xml";
                          echo "*** packaging: group: ${pom.groupId}, ${pom.artifactId}, ${pom.version}, ${pom.packaging}";
                          // Find built artifact under target folder
                          //filesByGlob = findFiles(glob: "target/*.${pom.packaging}");
                          filesByGlob = findFiles(glob: "webapp/target/*.{war,pom}");
 /*                          for file in filesByGlob:
                            echo """
                            ${filesByGlob[0].name}   ${filesByGlob[0].path}   ${filesByGlob[0].directory}
                            """ */

                          //filesByGlob = findFiles(glob: "**/${pom.version}/*${pom.version}.${pom.packaging}");
                          // Print some info from the artifact found
                          echo "${filesByGlob[0].name} ${filesByGlob[0].path} ${filesByGlob[0].directory} ${filesByGlob[0].length} ${filesByGlob[0].lastModified}"
                          // Extract the path from the File found
                          artifactPath = filesByGlob[0].path;
                          // Assign to a boolean response verifying If the artifact name exists
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
                                   // Artifact generated such as .jar, .ear and .war files.
                                      [artifactId: pom.artifactId,
                                      classifier: 'debug',
                                      file: artifactPath,
                                      type: pom.packaging],

                                      // Lets upload the pom.xml file for additional information for Transitive dependencies
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
