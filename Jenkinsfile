podTemplate(yaml: '''
        apiVersion: v1
        kind: Pod
        spec:
#           securityContext:
#             runAsUser: 1000
          containers:
          - name: maven
            image: maven:3.8.1-jdk-8
            command:
            - cat
            tty: true
#             securityContext:
#               privileged: true
          - name: kubectl
            image: lachlanevenson/k8s-kubectl:v1.8.8
            command:
            - cat
            tty: true
#            securityContext:
#              privileged: true
          - name: docker
            image: docker
            tty: true
#             securityContext:
#               privileged: true
            volumeMounts:
              - name: dockersock
                mountPath: "/var/run/docker.sock"
          volumes:
          - name: dockersock
            hostPath:
              path: /var/run/docker.sock  
''') {

    def containerName="javaapp"
    def tag="2-11-22-build-1"
    def dockerHubUser="developergo"
//     def dockerHubPwd="Welcome@2022$#"
    def httpPort="8090"

    node(POD_LABEL) {
        
//         stage('Checkout SCM') {
//             container('maven') {
//                 sh "git clone -b dev https://github.com/developergo2k18/DockerPipeline.git"
//                 stage('Build a Maven project') {
//                     sh "mvn clean package -f /home/jenkins/agent/workspace/JavaApp1/DockerPipeline/pom.xml"
//                 }
//             }
//         }

//         stage('Image Build') {
//             container('docker') {
//                 sh "docker system prune -f"
//                 docker.build("$containerName:$tag","/home/jenkins/agent/workspace/JavaApp1/DockerPipeline")
//                 echo "Image build complete"
//             }
//         }

//         stage('Push to Docker Registry'){
//             container('docker') {
// 		sh "docker login -u $dockerHubUser -p Welcome@2022${'$'}\\#"
//                 sh "docker tag $containerName:$tag $dockerHubUser/$containerName:$tag"
//                 sh "docker push $dockerHubUser/$containerName:$tag"
//                 echo "Image push complete"
//             }
//         }


        stage('Deploy App To Kubernetes Cluster'){
		withKubeConfig([credentialsId: 'kubelogin']) {
	          sh 'echo $KUBECONFIG'
                  sh 'cat $KUBECONFIG'
		  container('kubectl') {
		    sh 'kubectl get pods -n devops-tools'
	            sh 'kubectl version'
		  }
		}
        }

    }
}


