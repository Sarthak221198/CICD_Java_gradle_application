pipeline{

    agent any
    environment{
        VERSION = "${env.BUILD_ID}"
    }

    stages{

        stage("sonar quality check"){

            steps{

                script{

                    withSonarQubeEnv(credentialsId: 'sonar-token') {


                        sh 'chmod +x gradlew'

                        sh './gradlew sonarqube'

                    }
                     timeout(time: 1, unit: 'HOURS') {
                      def qg = waitForQualityGate()
                      if (qg.status != 'OK') {
                           error "Pipeline aborted due to quality gate failure: ${qg.status}"
                      }
                    }

                }

            }

        }
        stage("Docker build & docker push"){
            steps{
                script{
                    withCredentials([string(credentialsId: 'nexus_pass', variable: 'nexus-pass')]) {
                    sh  '''
                        docker build -t 18.213.3.117:8083/springapp:${VERSION} .
                        docker login -u admin -p $nexus-pass 18.213.3.117:8083
                        docker push 18.213.3.117:8083/springapp:${VERSION}
                        docker rmi 18.213.3.117:8083/springapp:${VERSION}
                    
                        '''                       
                    }
                }
            }
        }

    }

}
