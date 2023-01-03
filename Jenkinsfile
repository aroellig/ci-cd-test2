node {
    def server
    def buildInfo
    def rtMaven
    
    stage ('Clone') {
        git url: 'https://github.com/jfrog/project-examples.git'
    }
 
    stage ('Artifactory configuration') {
        // Obtain an Artifactory server instance, defined in Jenkins --> Manage Jenkins --> Configure System:
        server = Artifactory.server "cicd-test"

        rtMaven = Artifactory.newMavenBuild()
        rtMaven.tool = M3 // Tool name from Jenkins configuration
        rtMaven.deployer releaseRepo: test-libs-release-local, snapshotRepo: test-libs-snapshot-local, server: server
        rtMaven.resolver releaseRepo: test-libs-release-local, snapshotRepo: test-libs-snapshot-local, server: server
        rtMaven.deployer.deployArtifacts = false // Disable artifacts deployment during Maven run

        buildInfo = Artifactory.newBuildInfo()
    }
 
    stage ('Test') {
        rtMaven.run pom: 'maven-examples/maven-example/pom.xml', goals: 'clean test'
    }
        
    stage ('Install') {
        rtMaven.run pom: 'maven-examples/maven-example/pom.xml', goals: 'install', buildInfo: buildInfo
    }
 
    stage ('Deploy') {
        rtMaven.deployer.deployArtifacts buildInfo
    }
        
    stage ('Publish build info') {
        server.publishBuildInfo buildInfo
    }
}

