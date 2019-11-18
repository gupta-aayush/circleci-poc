def env = ''
def subenv = ''
def buildinfo = ''
pipeline {
  agent {
    docker {
      image '455589372314.dkr.ecr.eu-central-1.amazonaws.com/omnia-srv-base:0.0.2'
      args '-u 0:0'
    }
  }
  stages {
    stage('Pushing Artifacts to Octopus') {
      steps{
        dir('basecode')
          {
            script{
              subenv = currentBuild.projectName
              buildinfo = currentBuild.number
            }
            sh 'pwd'
            sh 'ls -lah'
            sh 'npm install'
            sh "zip -r omnia-${subenv}-fe.1.0.${buildinfo}.zip ./*"
            sh '''
            
            token="04675c595f1fffd7d77a492400742b79d9a27b92"
            # Create a release
            release=$(curl -XPOST -H "Authorization:token $token" --data "{\\"tag_name\\": \\"v1.0.0\\", \\"target_commitish\\": \\"master\\", \\"name\\": \\"v1.0.0\\", \\"body\\": \\"Omnia Spike\\", \\"draft\\": false, \\"prerelease\\": true}" https://git.sourceai.io/api/v3/repos/OMNIA/octopus-spike/releases)
            
            # Extract the id of the release from the creation response
            id=$(echo "$release" | sed -n -e 's/"id":\\ \\([0-9]\\+\\),/\\1/p' | head -n 1 | sed 's/[[:blank:]]//g')
            
            # Upload the artifact
            curl -XPOST -H "Authorization:token $token" -H "Content-Type:application/octet-stream" --data-binary @"omnia-$subenv-fe.1.0.$buildinfo.zip" "https://git.sourceai.io/api/v3/repos/OMNIA/octopus-spike/releases/$id/assets?name=omnia-$subenv-fe.1.0.$buildinfo.zip"
            '''
            //sh  "curl -X POST https://omnia-sandbox.octopus.app/api/packages/raw -H \"X-Octopus-ApiKey: API-BWRSCC6HPTW23YIHGO0LXD9UTDQ\" -F data=@omnia-${subenv}-fe.1.0.${buildinfo}.zip"
          }
      }
    }
  }
  post{
    always{
      echo 'Pipeline Completed!'
    }
    success {
      echo 'Pipeline Succeeded!'
    }
    unstable {
      echo 'Pipeline is Unsatble'
    }
    failure {
      echo 'Pipeline Failed. Check the Console Logs'
    }
  }
}
