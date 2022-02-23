pipeline{
    //Directives
    agent any
    tools {
        maven 'maven'
    }
    environment{
       ArtifactId = readMavenPom().getArtifactId()
       Version = readMavenPom().getVersion()
       Name = readMavenPom().getName()
       GroupId = readMavenPom().getGroupId()
    }
    stages {
        // Specify various stage with in stages

        // stage 1. Build
        stage ('Build'){
            steps {
                sh 'mvn clean install package'
            }
        }

        // Stage2 : Testing
        stage ('Test'){
            steps {
                echo ' testing......'

            }
        }

        // Stage3 : Publish the artifact to Nexus
        stage ('Publish to Nexus'){
            steps {
                nexusArtifactUploader artifacts: 
                [[artifactId: 'VinayDevOpsLab', 
                classifier: '', 
                file: 'target/VinayDevOpsLab-0.0.5-SNAPSHOT.war', 
                type: 'war']], 
                credentialsId: '2dc2cc82-8188-44db-8fc1-1c4642cc29a1', 
                groupId: 'com.vinaysdevopslab', 
                nexusUrl: '34.200.226.162:8081', 
                nexusVersion: 'nexus3', 
                protocol: 'http', 
                repository: 'VinaysDevOpsLab-SNAPSHOT', 
                version: '0.0.5-SNAPSHOT'
            }
        }

        // Stage3 : Deploying
        stage ('Deploy'){
            steps {
                echo ' deploying.......'
            }
        }

        // Stage 4 : Print some information
        stage ('Print Environment variables'){
            steps{
                echo "Artifact ID is '${ArtifactId}'"
                echo "Version is '${Version}'"
                echo "GroupID is '${GroupID}'"
                echo "Name is '${Name}'"
            }
        }
    }

}