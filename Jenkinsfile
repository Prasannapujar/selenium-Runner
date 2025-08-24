pipeline{

    agent any
    parameters {
      choice choices: ['chrome', 'firefox'], description: 'Please select the browser', name: 'BROWSER'
    }

    stages{

        stage('Start Grid'){
            steps{
                sh "docker-compose -f grid.yaml up -d --scale ${params.BROWSER}=2 -d"
            }
        }

        stage('Run Test'){
            steps{
                sh "docker-compose -f test-suites.yaml up --pull=always"
                script{
                  if(fileExists('output/vendor-portal/testng-failed.xml') || fileExists('output/flight-reservation/testng-failed.xml')){
                    error("There are test failures, please check the reports")
                  } else {
                    echo "All tests passed successfully"
                  }
            }
        }

    }

    post {
        always {
            sh "docker-compose -f grid.yaml down"
            sh "docker-compose -f test-suites.yaml down"
            archiveArtifacts artifacts: 'output/flight-reservation/emailable-report.html', followSymlinks: false
            archiveArtifacts artifacts: 'output/vendor-portal/emailable-report.html', followSymlinks: false
        }
    }

}