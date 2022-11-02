podTemplate(containers: [
    containerTemplate(
        name: 'maven', 
        image: 'maven:3.8.1-jdk-8', 
        command: 'cat',
        ttyEnabled: true
        ),
    containerTemplate(
        name: 'docker', 
        image: 'docker', 
        command: 'cat', 
        ttyEnabled: true)
  ],
  volumes: [
      hostPathVolume(hostPath: '/var/run/docker.sock', 
                     mountPath: '/var/run/docker.sock'),
      hostPathVolume(hostPath: '/home/docker/repository', 
                     mountPath: '/root/.m2/repository'),
  ]) {

    def containerName="javaapp"
    def tag="2-11-22-build-1"
    def dockerHubUser="developergo"
    def dockerHubPwd="Welcome@2022\$#"
    def httpPort="8090"

    node(POD_LABEL) {
        
        environment {
            dockerhub=credentials("dockerHubAccount")
        }
        
        stage('Checkout SCM') {
            container('maven') {
                sh "git clone -b dev https://github.com/developergo2k18/DockerPipeline.git"
                stage('Build a Maven project') {
                    sh "mvn clean package -f /home/jenkins/agent/workspace/JavaApp1/DockerPipeline/pom.xml"
                }
            }
        }

        stage('Image Build') {
            container('docker') {
                sh "docker system prune -f"
                docker.build("$containerName:$tag","/home/jenkins/agent/workspace/JavaApp1/DockerPipeline")
                echo "Image build complete"
            }
        }

        stage('Push to Docker Registry'){
            container('docker') {
                sh "docker login -u $dockerhub_USR -p $dockerhub_PSW"
                sh "docker tag $containerName:$tag $dockerUser/$containerName:$tag"
                sh "docker push $dockerUser/$containerName:$tag"
                echo "Image push complete"
            }
        }


        stage('Deploy App To Kubernetes Cluster'){
            kubernetesApply(file: '/home/jenkins/agent/workspace/JavaApp1/DockerPipeline/deploy.yml')
        }

    }
}


