node {
    def image

    stage('Clean') {
        sh 'rm * -f -r -d -v'
    }

    stage('cloneRepository') {
        checkout scm
    }

    stage('pullImage') {
        image = docker.image("${params.imageName}")
        docker.withRegistry("${params.registryName}") {
            image.pull()
        }
    }

    stage('Scan image') {
        try {
            prismaCloudScanImage ca: '', cert: '', dockerAddress: 'unix:///var/run/docker.sock', ignoreImageBuildTime: true, image: "${imageName}", key: '', logLevel: 'debug', podmanPath: '', project: '', resultsFile: 'prisma-cloud-scan-results.json'
        } finally {
            prismaCloudPublish resultsFilePattern: 'prisma-cloud-scan-results.json'
        }
    }

    stage('pushImage') {
        docker.withRegistry("${ProdregistryName}") {
            image.push()
        }
    }
    
    stage('removeImage') {
        sh "docker image rm ${imageName}"
    }                
}
