pipeline {
    environment {
        CONTAINER_REGISTRY = "bosch.azurecr.io"
        IMAGE_ID = "$CONTAINER_REGISTRY/bosch-data-service"
    }
    stages {
        stage ("Build and push") {
            agent { label "dockeredge" }
            stages {
                stage ("Build") {
                    steps {
                        sh '''
                            ./gradlew bosch-data-service:clean bosch-data-service:build
                        '''
                    }
                }
                stage ("Integration tests") {
					steps {
						sh '''
						  	 ./gradlew unpack bosch-data-service-integration:clean bosch-data-service-integration:build
					    '''
	                }
                }
                stage ("Push image and prepare deploy") {
                   steps {
                        sh '''
                          docker login -u test-user -p test-pwd $CONTAINER_REGISTRY
                          ./gradlew dockerPush
                        '''
                    }
                }
            }
        }
        stage ("Deploy") {
            agent { label deployAgent }
            steps {
              sh '''
                kubectl apply -f bosch-data-service-deploy/k8s-manifest
              '''
            }
        }
    }
}

