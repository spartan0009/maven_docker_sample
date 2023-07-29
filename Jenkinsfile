pipeline {
      agent
	{
  		node
  		{
  			label 'docker-slave'
  		}
	}

    triggers {
      pollSCM ''
    }
     environment
    { 

        PROJECT = 'svc-apm-blik'
        IMAGE = 'sample-springboot-app:latest'
		DOCKERREPO= 'bmaddi' 
        ARTIURL = 'artifactory-new-93bf8f5a14c88dd6.elb.eu-west-2.amazonaws.com:8082'

    }
    stages {
    stage('Build preparations')
        {
            steps
            {
                script
                {
				     // calculate GIT lastest commit short-hash
                    gitCommitHash = sh(returnStdout: true, script: 'git rev-parse HEAD').trim()
                    shortCommitHash = gitCommitHash.take(7)
                    // calculate a sample version tag
                    VERSION = shortCommitHash
                    currentBuild.displayName = "${PROJECT}-${VERSION}"
                    IMAGE = "$DOCKERREPO:${BUILD_NUMBER}.${VERSION}"
                }
            }
        }
            stage('Docker build')
        {
            steps
            {
                script
                {
                    // Build the docker image using a Dockerfile
                    docker.build("$IMAGE","--no-cache -f Dockerfile .")
                }
            }
        }

               stage('Docker push')
        {
            steps
            {
                script
                {
                     withDockerRegistry(credentialsId: 'docker-hub-creds', url: 'https://registry.hub.docker.com')
                    {
                    /* groovylint-disable-next-line NestedBlockDepth */
                       docker.image(IMAGE).push()
                    }
                }
            }
    }
}
}