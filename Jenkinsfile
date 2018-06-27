node ('maven') {
    stage('Initialisation') {
        git credentialsId: 'c5abe1ef6e063350701ca080ff3bcc761c7906ff', url: 'https://github.com/ianpurdy/inventory-wildfly-swarm.git'
    }
    stage('Build JAR') {
        sh "mvn package"
        stash name:"jar", includes:"target/inventory-1.0-SNAPSHOT-swarm.jar"
    }
    stage('Build Image') {
        unstash name:"jar"
        openshift.withCluster() {
          openshift.startBuild("inventory-s2i", "--from-file=target/inventory-1.0-SNAPSHOT-swarm.jar", "--wait")
        }
    }
    stage('Deploy') {
        openshift.withCluster() {
          def dc = openshift.selector("dc", "inventory")
          dc.rollout().latest()
          dc.rollout().status()
        }
    }
}

