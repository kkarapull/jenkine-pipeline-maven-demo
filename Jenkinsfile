#!groovy

pipeline {
    agent any

    tools {
        maven "maven3.8.4" // You need to add a maven with name "3.6.0" in the Global Tools Configuration page
        jdk 'jdk9' 
    }

    stages {
        stage("Build") {
            steps {
                sh "mvn -version"
                sh "mvn clean install"
                  }
                        }
      
      stage('Unit Tests') {
	    // Run Unit tests 
            steps{
                sh 'mvn -f pom.xml clean test'
            }
        }	
        stage ('Artifactory configuration') {
            steps {
		// specify Artifactory server
                rtServer (
                    id: "ARTIFACTORY_SERVER",
                    url: "http://172.18.0.4:8081",
		    credentialsId: 'Admin.Artifactory'
                )
		// specify the repositories to be used for deploying the artifacts in the Artifactory
                rtMavenDeployer (
                    id: "MAVEN_DEPLOYER",
                    serverId: "ARTIFACTORY_SERVER",
                    releaseRepo: "libs-release-local",
                    snapshotRepo: "libs-snapshot-local"
                )
		// defines the dependencies resolution details
                rtMavenResolver (
                    id: "MAVEN_RESOLVER",
                    serverId: "ARTIFACTORY_SERVER",
                    releaseRepo: "libs-release",
                    snapshotRepo: "libs-snapshot"
                )
            }
        }
        stage ('Build & Upload Artifact') {
	    // run Maven Build and upload the built artifact to Artifactory
            steps {
                rtMavenRun (
                    tool: "maven3.8.4", // Tool name from Jenkins configuration
                    pom: 'pom.xml',
                    goals: 'clean install',
                    deployerId: "MAVEN_DEPLOYER",
                    resolverId: "MAVEN_RESOLVER"
                )
            }
        }
        stage ('Publish build info') {
	    // Publish the build info in the Artifactory
            steps {
                rtPublishBuildInfo (
                    serverId: "ARTIFACTORY_SERVER"
                )
            }
        }
  
    
    }
  
  

    post {
        always {
            cleanWs()
        }
    }
}
