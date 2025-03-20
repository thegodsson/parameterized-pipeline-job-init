pipeline {
  agent any
  stages {
    stage('Maven Version') {
      steps {
        sh 'echo Print Maven Version'
        sh 'mvn -version'
        sh "echo Sleep-Time - ${params.SLEEP_TIME}, Port - ${params.APP_PORT}, Branch - ${params.BRANCH_NAME}"
      }
    }

    stage('Build') {
      steps {
        sh 'mvn clean package -DskipTests=true'
        archiveArtifacts 'target/hello-demo-*.jar'
      }
    }

    stage('Test') {
      steps {
        sh 'mvn test'
        junit(testResults: 'target/surefire-reports/TEST-*.xml', keepProperties: true, keepTestNames: true)
      }
    }

       stage('Local Deployment') {
  steps {
    sh """
      nohup java -jar target/hello-demo-*.jar > app.log 2>&1 &
      sleep 5
      ps aux | grep hello-demo | grep -v grep
    """
  }
}
    
    stage('Integration Testing') {
      steps {
        sh "sleep ${params.SLEEP_TIME}"
        sh '''
        counter=0
        while ! curl -s http://localhost:${params.APP_PORT}/hello; do
        counter=$((counter+1))
        if [ "$counter" -ge 10 ]; then
        echo "L'application ne répond pas après 10 essais"
        exit 1
        fi
        echo "Attente que l'application démarre..."
        sleep 3
        done
    '''
  }
}
    



  }
  tools {
    maven 'M398'
  }

}
