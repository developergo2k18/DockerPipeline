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
        stage('Checkout SCM') {
            container('maven') {
                checkout scm
                stage('Build a Maven project') {
                    sh "mvn clean package -f /home/jenkins/agent/workspace/deploykube/DockerPipeline/pom.xml"
                }
            }
        }

        stage('Image Build') {
            container('docker') {
                sh "docker system prune -f"
                sh "docker build -t $containerName:$tag  -t $containerName --pull --no-cache ."
                echo "Image build complete"
            }
        }

        stage('Push to Docker Registry'){
            container('docker') {
                withCredentials([usernamePassword(credentialsId: 'dockerHubAccount', usernameVariable: $dockerHubUser, passwordVariable: $dockerHubPwd)]) {
                    sh "docker login -u $dockerUser -p $dockerPassword"
                    sh "docker tag $containerName:$tag $dockerUser/$containerName:$tag"
                    sh "docker push $dockerUser/$containerName:$tag"
                    echo "Image push complete"
                }
            }
        }


        stage('Deploy App To Kubernetes Cluster'){
            kubernetesApply(file: 'DockerPipeline/deploy.yml')
        }

    }
}


