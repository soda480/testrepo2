pipeline {
    agent {
        label 'rbhe'
    }
    stages {
        stage('SDLE Upload') {
            steps {
                script {
                    parallel getStages('sdle.yaml')
                }
            }
        }
    }
    post {
        cleanup {
            cleanWs()
        }
    }
}

def getStages(filename) {
    def projects = readYaml file: filename
    def stages = projects.collectEntries {
        ["${it}" : generateStage(it)]
    }
    stages
}

def generateStage(project) {
    return {
        stage("${project.name}") {
            // ensure isolated workspace for each project
            ws {
                sh 'rm -rf artifacts/'

                downloadGHArtifacts([
                    projectOwner: project.owner,
                    projectRepo: project.repo,
                    projectBranch: project.branch,
                    projectArtifacts: project.artifacts,
                    projectFolders: project.folders])

                // sdleUpload([
                //    sdleUploadProjectId: project.sdle_project_id]) 
            }
        }
    }
}