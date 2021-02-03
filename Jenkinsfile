node {
    def image

    stage('Clean') {
        sh 'rm * -f -r -d -v'
    }

    stage('cloneRepository') {
        checkout scm
    }

    stage('pullImage') {
        image = docker.image("${params.registryName}/${params.imageName}")
        image.pull()
    }

    stage('Scan image') {
        try {
            prismaCloudScanImage ca: '', cert: '', dockerAddress: 'unix:///var/run/docker.sock', ignoreImageBuildTime: true, image: "${registryName}/${imageName}", key: '', logLevel: 'debug', podmanPath: '', project: '', resultsFile: 'prisma-cloud-scan-results.json'
        } finally {
            prismaCloudPublish resultsFilePattern: 'prisma-cloud-scan-results.json'
        }
    }

    stage('pushImage') {
        docker.withRegistry("${ProdregistryName}") {
            image.push("${params.imageName}")
        }
    }
    
    stage('removeImage') {
        sh "docker image rm ${registryName}/${imageName}"
    }                
}
