pipeline {
    agent any
environment { 
   VERSION = "${BUILD_NUMBER}"
   IMAGEN = "jenkins"
   GRUPO_RECURSOS="PruebasConDocker"
   REGISTRY = "registrydockerj"
   SERVIDOR = "registrydockerj.azurecr.io"
   KUBERNETES = "KubernetesDocker"
   SUSCRIPCION ="57909e56-3cb2-4cbd-a4f6-93e3ed70d320"
   REPO = "https://github.com/juanfranciscobumo/pruebasDocker.git"
   RAMA = "master"
}
    stages {
        stage('Descargar codigo') {
            steps {
                echo 'Descargando código...'
                git url: "${REPO}", branch: "${RAMA}"
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying....'
				echo "Running ${VERSION} on ${JENKINS_URL}"
				bat "docker build -t ${SERVIDOR}/${IMAGEN}:${BUILD_NUMBER} ."
                echo 'Logueandose en azure...'
				bat "az acr login -n ${REGISTRY}"
				echo 'Haciendo push...'
                bat "docker push ${SERVIDOR}/${IMAGEN}:${BUILD_NUMBER}"
            }
        }
		        stage('Publish') {
            steps {
                echo 'Publicando artefacto....'
				echo 'Remplanzando versión en el archivo...'
contentReplace(
    configs: [
        fileContentReplaceConfig(
            configs: [
                fileContentReplaceItemConfig(
                    search: 'BUILD_NUMBER',
                    replace: "${BUILD_NUMBER}",
                    matchCount: 1)
                ],
            fileEncoding: 'UTF-8',
            filePath: 'deploymentJenkins.yml')
        ])      
		        echo 'Iniciando sesión en azure...'
		        bat "az aks get-credentials -n ${KUBERNETES} --resource-group ${GRUPO_RECURSOS} --subscription ${SUSCRIPCION}"
                echo 'Desplegando artefacto...'
				bat "kubectl apply -f deploymentJenkins.yml"
				
            }
        }
    }
}