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
//     def dockerHubPwd="Welcome@2022$#"
    def httpPort="8090"

    node(POD_LABEL) {
        
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
		sh "docker login -u $dockerHubUser -p Welcome@2022${'$'}\\#"
                sh "docker tag $containerName:$tag $dockerHubUser/$containerName:$tag"
                sh "docker push $dockerHubUser/$containerName:$tag"
                echo "Image push complete"
            }
        }


        stage('Deploy App To Kubernetes Cluster'){
//             kubernetesApply(file: '/home/jenkins/agent/workspace/JavaApp1/DockerPipeline/deploy.yml', environment: 'testns')
		kubeconfig(caCertificate: 'LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUU2RENDQXRDZ0F3SUJBZ0lRRXBzcmVZV0JzMmRHeXJHZldESmxpekFOQmdrcWhraUc5dzBCQVFzRkFEQU4KTVFzd0NRWURWUVFERXdKallUQWdGdzB5TWpBM016QXdPREkzTXpCYUdBOHlNRFV5TURjek1EQTRNemN6TUZvdwpEVEVMTUFrR0ExVUVBeE1DWTJFd2dnSWlNQTBHQ1NxR1NJYjNEUUVCQVFVQUE0SUNEd0F3Z2dJS0FvSUNBUUNqCndwQm85bVFOdTA2Q3I2SjUzTGFwZG5lSXdMTVhGN2txcVpuNURwOXJ2NkQxOWpjUDZJaTVDdXJTdTd5ajViWlYKSFo1dUFnWkhFV1Rid1BQTElLdlUzdFllRWRjbFc3QUlRcmtXNFNidU82bzRQTVJ1RGJlekJzRkhYTkxveW00SgpjN0hWcGMrVERpYTRPYlV4T2dMcHZLRlc1NzRFUDdXazNBNmg0cThyUyttQlp1ZEhJSjdCQktXMldDV0U3V2pLCmN6QnRnRUVJTngzQzJ1cUJLUVUzSnAzVVpCY0s4anFjNEdPTGV5NjVQV1JPL29Wc2VEUnZldHoxdW1xc0tDOFQKMk1GdkMwYTlNQmFnc2dDMm8xcm1rcnRoQ21KRm9OcHNxQXBmdnhxR1N4TlJxR005eFJENFljc2J6V0txOVdOZApEY2xzM2Jpd3pCYVdTWE80NXc0eG55bHp4R0hKWGpiSHlWVStnSU80QkdqcE9pWTA2Q1VrNU9TTVA3ZXFYM2lyCnlCZEpidllBYU5WeXh5ZHowK3grRm5GdGVRVGFzNEpDdm5pUFBTZ3NqQ0xSaDc1dGlqOUEyU2k0NWZ3YWdLNDQKSm5GRGNLeXdrRnBKdGdIYVk1dXIxRnl5TTFpMFdXWHFobnJpbFB3T01qYng1VXVUNVF1c3NwaDJFakRiT2R4KwpNWWRJNkdqcGJLakxNb1kvcWt4dkZ4dkxQYmFrUmQxRzRWT0FYV25ORVhYNFZ6TXVkcWxUM20yd2U4Zk1jUlhyClMwY3cvb09ycHg1VHRiYnRrbGt4c3Jib3k3SVB6bWJZRnY5Y0pyTkMwZUJJT2t0ZndpQjFZQ0FaMWN5UmNxRjIKYU9EcEV0M2RhazM0NFByWjFBem1rNytMUVBnTDkvRUM2ZXgyODZ5M09RSURBUUFCbzBJd1FEQU9CZ05WSFE4QgpBZjhFQkFNQ0FxUXdEd1lEVlIwVEFRSC9CQVV3QXdFQi96QWRCZ05WSFE0RUZnUVU0YmZhVTlrSFZaWEJFKzZsCldGbFduOElpS0Jjd0RRWUpLb1pJaHZjTkFRRUxCUUFEZ2dJQkFHOW8xc3RFMlJaejJiby9Ma3U1SCswNURjTzIKRkwvWm9KRWg3Yy9YUW81WUEyWUlsUk1WT3c2RitjRnF0cGY4TkZkUFN4OFVmbXl4b3B6dXkxekM4WkVFUDBOawpNcGQ1OWJzYWpDaG5hcTBLTmZHRm5lUjRtckgzTG9pZ0VDdDgzK09ZbjRnUUpySkt5dkhDK2h6cmZXSE52ZU10CnlqZ0MwNi9GL3BCWnhjdUw0cEUvNFVhelFkZWFKaWo3alRoVitEallGdGJNMTR0bndHQUwrUUoxN3g5QnFBVmwKLzdqOHE3VUh2REhaWHhNNklOTG1QbEl3OUhpOEZkL2JPVnZLL1A3U2ZQbFo2TWxZMEVZNVc2a0tJbFhDWnZMMApGQ29ORzZwdkFmRHAxMUpoOVlMQ3FsOEszMFRjQXVJS2xEVzN6TXdHZU1zY1h2Z29yNWhuYkR6czNhZEJ0bWl3Ck42bG9pWmlTWXVNK0VVdTgrMVVxbExObEU3eDYzMlBWTlJyVUk5ZG03Tk1oTTdmcUUwUU5yQTR1dGdyWE1jL2EKdUx5OUVRb3VVTEVyT1R3NDAyODV2MWhscmI3dEtRY3RyQnBzcGg2K1pyZUxzaFAwSGVjbE9aQTRGNHFMK1Z1WApveDg2MmhQTnJBMklZNlc1ZjlCV1ZBbmNJczgxZ2VDbDU3WUpiRlAxZmp4NUw4U3phNEwweE1ONmVoTFp3UjNTCnlIQ3NESWdFdFlFQWZGL3h1NkVwZW5ZWHBVVXhCcHF3VXlUU1BuQTBMZ3VyUHI4SDlHaFpLcVpudzRFRjFrUDIKalZDMmYzUTVRaEF4RTZBTS9qNDdvM2ozYjV2V3FQTElLRWtFUGdXY0tScUlrcllLTVpHVmYzZ2twbEhqZkVSQQpwa3hYSGd3R2RWTFdrbDVECi0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K', serverUrl: 'https://127.0.0.1:33982') {
                    sh 'kubectl get pods'
		}
        }

    }
}


