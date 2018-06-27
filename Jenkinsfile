node ('maven') {
    stage('Build JAR') {
        sh "mvn package"
        stash name:"jar", includes:"target/inventory-1.0-SNAPSHOT-swarm.jar"
    }
    stage('Build Image') {
        unstash name:"jar"
        script {
          openshift.withCluster() {
            openshift.startBuild("inventory-s2i", "--from-file=target/inventory-1.0-SNAPSHOT-swarm.jar", "--wait")
          }
        }
    }
    stage('Deploy') {
        script {
          openshift.withCluster() {
            def dc = openshift.selector("dc", "inventory")
            dc.rollout().latest()
            dc.rollout().status()
          }
        }
    }
}
