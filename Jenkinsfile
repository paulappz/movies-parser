def imageName = 'paulappz/movies-parser'
def registry = 'https://registry.gbnlcicd.com'

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
        docker.build(imageName)
    }

    stage('Push'){
   //     docker.withRegistry(registry, 'registry') {
    //        docker.image(imageName).push(commitID())

      //      if (env.BRANCH_NAME == 'develop') {
      //          docker.image(imageName).push('develop')
     //       }
     //   }
    }

    stage('Analyze'){
     //   def scannedImage = "${registry}/${imageName}:${commitID()} ${workspace}/Dockerfile"
     //   writeFile file: 'images', text: scannedImage
      //  anchore name: 'images'
    }
}

def commitID() {
    sh 'git rev-parse HEAD > .git/commitID'
    def commitID = readFile('.git/commitID').trim()
    sh 'rm .git/commitID'
    commitID
}