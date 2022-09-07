pipeline {

    agent any

    tools {

        maven "MAVEN"
    }
    
    environment {
        NEXUS_VERSION = "nexus3"
        NEXUS_PROTOCOL = "http"
        NEXUS_URL = "192.168.1.3:8081"
        NEXUS_REPOSITORY = "java-app"
        NEXUS_CREDENTIAL_ID = "nexusadmin"
    }

    
    stages {
        stage('Clone code from GitHub') {
            steps{
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'gitcred', url: 'https://github.com/sergeikitsun/java-simple-app.git']]])
                sh "mvn -Dmaven.test.failure.ignore=true clean package"
                sh "ls -lha"
                sh "pwd"
                sh "whoami"
                

            }
        }
        
        
        stage("Maven Build") {
            steps {
                script {
                    sh "ls -lha"
                    sh "pwd"
                    sh "whoami"
                    sh "mvn -Dmaven.test.failure.ignore=true clean package"
                    sh "whoami"
                }
            }
        }    
        
        stage('Publish to Nexus Repository Manager') {
            steps {
                script {
                    pom = readMavenPom file: "pom.xml";
                    filesByGlob = findFiles(glob: "target/*.${pom.packaging}");
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
                                classifier: '',
                                file: artifactPath,
                                type: pom.packaging],
                                [artifactId: pom.artifactId,
                                classifier: '',
                                file: "pom.xml",
                                type: "pom"]
                            ]
                        );
                        echo "It doesn't work"
                    }
                    else {
                        error "*** File: ${artifactPath}, could not be found";
                    }
                    
                }
                
            }
        }
    }
    
}