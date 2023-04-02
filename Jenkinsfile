def imageName = 'paulappz/movies-parser'

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
                imageTest.inside{
                    sh 'go test'
                }
            },
            'Security Tests': {
                imageTest.inside('-u root:root'){
                    sh 'nancy /go/src/github/paulappz/movies-parser/Gopkg.lock'
                }
            }
        )
    }
}

def commitID() {
    sh 'git rev-parse HEAD > .git/commitID'
    def commitID = readFile('.git/commitID').trim()
    sh 'rm .git/commitID'
    commitID
}