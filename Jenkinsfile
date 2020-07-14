pipeline {
    agent any
    tools {
        go 'Go1.14'
    }
    environment {
        GO114MODULE = 'on'
        CGO_ENABLED = 0 
        GOPATH = "${JENKINS_HOME}/jobs/${JOB_NAME}/builds/${BUILD_ID}"
    }
    stages {        
        stage('Pre Test') {
            steps {
                echo 'Installing dependencies'
                sh 'go version'
                sh 'go get -d ./'
            }
        }
        
        stage('Build') {
            steps {
                echo 'Compiling and building'
                sh 'go build'
            }
        }

        stage('Test') {
            steps {
                withEnv(["PATH+GO=${GOPATH}/bin"]){
                    echo 'Running vetting'
                    sh 'go vet .'
                    echo 'Running test'
                    sh 'go test -v'
                }
            }
        }
        stage('SSH transfer') {
        steps {
            sshPublisher(
                continueOnError: false, failOnError: true,
                publishers: [
                    sshPublisherDesc(
                            configName: "Zerund",
                            verbose: true,
                            transfers: [
                            sshTransfer(
                            sourceFiles: "workspace/birdpedia ci/birdpedia",
                            removePrefix: "workspace/birdpedia ci",
                            remoteDirectory: "/",
                            execCommand: "echo oho"
                        )
                    ])
                ])
        }
	}
	stage('Done'){
		steps{
			echo 'Done with builds and tests'
		}	
	}
        
    }
}
