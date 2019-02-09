withCredentials([
  [
    $class          : 'UsernamePasswordMultiBinding',
    credentialsId   : 'artifactory-cloud',
    passwordVariable: 'ARTIFACTORY_PASSWORD',
    usernameVariable: 'ARTIFACTORY_USERNAME'

    
  ]
])

{


node ('Slave')
{
    
	
	stage('Checkout') {
				
//first commit				
checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'anishgithub', url: 'https://github.com/anishpravin/gameoflife.git']]])
			          }
			


 						 
	stage('maven build') {
	sh '''
	echo $ARTIFACTORY_USERNAME
	mvn --version
	mvn clean install
	'''
	                     }
						 
						 
	stage('push gameoflife.war to artifactory') {
	sh '''
    curl -u$ARTIFACTORY_USERNAME:$ARTIFACTORY_PASSWORD -T $WORKSPACE/gameoflife-web/target/gameoflife.war "https://anish123.jfrog.io/anish123/Gameoflife/pipeline-$BUILD_NUMBER"
	'''
												}
	
										 
										 
							  
												   
	stage ('build docker image') {
	            sh '''
				docker build -t gameoflife:pipeline-$BUILD_NUMBER $WORKSPACE/gameoflife-web/ --file $WORKSPACE/gameoflife-web/Dockerfile
				'''
				                }
								
	stage ('push gameoflifedocker image') {
	withDockerRegistry(credentialsId: 'artifactory-cloud', url: 'https://anish123-gameoflife-docker.jfrog.io') {
	            sh '''
				docker tag gameoflife:pipeline-$BUILD_NUMBER anish123-gameoflife-docker.jfrog.io/gameoflife:pipeline-$BUILD_NUMBER
				docker push anish123-gameoflife-docker.jfrog.io/gameoflife:pipeline-$BUILD_NUMBER
				'''
				                                                                                        }
										  }
										  
	stage ('run docker image') {
	            sh '''
				docker stop gameoflife-pipeline
				docker rm gameoflife-pipeline				
				docker run -d -p 8889:8080 --name gameoflife-pipeline anish123-gameoflife-docker.jfrog.io/gameoflife:pipeline-$BUILD_NUMBER
				'''
				                }
}

}



