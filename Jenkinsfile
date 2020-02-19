pipeline {
    environment {
	GRADLE_IMAGE = 'gradle:6.1.1-jdk11'
    }

    agent any
    stages {
	stage('Test') {
	    steps {
		    sh '''
		        docker run --rm -v $(pwd):/opt/app -v ~/.gradle:/root/.gradle -w /opt/app $GRADLE_IMAGE /bin/bash -c "gradle clean test"
		       '''
	    }
	}
	stage('Build') {
	    steps {
		    sh '''
		        docker run --rm -v $(pwd):/opt/app -v ~/.gradle:/root/.gradle -w /opt/app $GRADLE_IMAGE /bin/bash -c "gradle clean build"
		       '''
	    }
	}
	stage('Build Image') {
	    steps {
                script {
			dockerImage = docker.build(registry)
                        docker.withRegistry('https://registry.hub.docker.com', registryCredential ) {
                            dockerImage.push()
			}
                }
	    }
	}
	stage('Deploy to dev') {
	    steps {
		    sh '''
			kubectl apply -f ./deploy_k8s.yml -n $dev_env
			    '''
	    }
	}
    }
}
