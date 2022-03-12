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
                script {

                def NexusRepo = Version.endsWith("SNAPSHOT") ? "VinaysDevOpsLab-SNAPSHOT" : "VinaysDevOpsLab-RELEASE"
                nexusArtifactUploader artifacts: 
                [[artifactId: "${ArtifactId}", 
                classifier: '', 
                file: "target/${ArtifactId}-${Version}.war", 
                type: 'war']], 
                credentialsId: '2dc2cc82-8188-44db-8fc1-1c4642cc29a1', 
                groupId: "${GroupId}", 
                nexusUrl: '3.231.163.188:8081', 
                nexusVersion: 'nexus3', 
                protocol: 'http', 
                repository: "${NexusRepo}", 
                version: "${Version}"
                }
            }
        }

        // Stage 4 : Print some information
        stage ('Print Environment variables'){
            steps{
                echo "Artifact ID is '${ArtifactId}'"
                echo "Version is '${Version}'"
                echo "GroupID is '${GroupId}'"
                echo "Name is '${Name}'"
            }
        }


        // Stage 5 : Deploying the build artifact to Apache Tomcat
        stage ('Deploy to Tomcat'){
            steps {
                echo "Deploying......."
                sshPublisher(publishers: 
                [sshPublisherDesc(
                    configName: 'Ansible_Controller', 
                    transfers: [
                        sshTransfer(
                           cleanRemote: false,
                           execCommand: 'ansible-playbook /opt/playbooks/downloadanddeploy.yaml -i /opt/playbooks/hosts', 
                           execTimeout: 120000
                        )
                    ],
                    usePromotionTimestamp: false, 
                    useWorkspaceInPromotion: false, 
                    verbose: false)
                    ])
            
            }

        }



// Stage 6 : Deploying the build artifact to Docker
        stage ('Deploy to Docker'){
            steps {
                echo "deploying......."
                sshPublisher(publishers: 
                [sshPublisherDesc(
                    configName: 'Ansible_Controller', 
                    transfers: [
                        sshTransfer(
                           cleanRemote: false,
                           execCommand: 'ansible-playbook /opt/playbooks/downloadanddeploy_docker.yaml -i /opt/playbooks/hosts', 
                           execTimeout: 120000
                        )
                    ],
                    usePromotionTimestamp: false, 
                    useWorkspaceInPromotion: false, 
                    verbose: false)
                    ])
            }
        }
    }
}