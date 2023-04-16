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
        sh "aws ecr get-login-password --region ${region} | docker login --username AWS --password-stdin ${registry}/${imageName}"
        sh "docker build --tag ${imageName}:${commitID()} ."
        sh " docker tag ${imageName}:${commitID()} ${registry}/${imageName}:${commitID()}"
    }
    
    stage('Push'){
       sh "docker push ${registry}/${imageName}:${commitID()}"
    }

    stage('Analyze'){
           def scannedImage = "${registry}/${imageName}:${commitID()} ${workspace}/Dockerfile"
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