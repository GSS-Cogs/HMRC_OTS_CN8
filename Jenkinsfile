pipeline {
    agent {
        label 'master'
    }
    stages {
        stage('Clean') {
            steps {
                sh 'rm -rf out'
            }
        }
        stage('Prepare source') {
            agent {
                docker {
                    image 'cloudfluff/databaker'
                    reuseNode true
                }
            }
            steps {
                sh 'jupyter-nbconvert --to python --stdout Prepare_sources.ipynb | python'
            }
        }
        stage('Upload draftset') {
            steps {
                script {
		    jobDraft.replace()
                    def csvs = []
                    for (def file : findFiles(glob: 'out/*.csv')) {
                        csvs.add("out/${file.name}")
                    }
                    uploadTidy(['out/CN8_Non-EU_cod_2012-2016_0000.csv'], 'https://ons-opendata.github.io/ref_trade/columns.csv')
                }
            }
        }
        stage('Publish') {
            steps {
                script {
                    jobDraft.publish()
                }
            }
        }
    }
    post {
        always {
	    script {
                archiveArtifacts 'out/**'
	    }
        }
    }
}
