def imageName = 'paulappz/movies-parser'
// def registry = 'https://registry.gbnlcicd.com'
def registry = '530364773324.dkr.ecr.eu-west-2.amazonaws.com' 
def region = 'eu-west-2'

node('workers'){
    stage('Checkout'){
        checkout scm
    }

    def imageTest= docker.build("${imageName}-test", "-f Dockerfile.test .")

    stage('Pre-integration Tests'){
        parallel(
            'Quality Tests': {
                imageTest.inside{
                    sh 'golint'
                }
            },
            'Unit Tests': {
              //  imageTest.inside{
              //      sh 'go test'
              //  }
            },
            'Security Tests': {
             //   imageTest.inside('-u root:root'){
              //      sh 'nancy /go/src/github/paulappz/movies-parser/Gopkg.lock'
              //  }
            }
        )
    }

    stage('Build'){
       def imageBuild = docker.build(imageName)
    }
    
    stage('Push'){
       sh "aws ecr get-login-password --region ${region} | docker login --username AWS --password-stdin ${registry}/${imageName}"
     
       sh "  docker build -t ${imageName} . "
       sh " docker tag ${imageName}:latest ${registry}/${imageName}:latest"
       sh "docker push ${registry}/${imageName}:latest"

         //  imageBuild.push(commitID()) 
         //       if (env.BRANCH_NAME == 'develop') {
         //   imageBuild.push('develop')
         //        } 
    
}

    stage('Analyze'){
           def scannedImage = "${registry}/${imageName}:latest ${workspace}/Dockerfile"
           writeFile file: 'images', text: scannedImage
            anchore name: 'images'
    }
}

def commitID() {
    sh 'git rev-parse HEAD > .git/commitID'
    def commitID = readFile('.git/commitID').trim()
    sh 'rm .git/commitID'
    commitID
}