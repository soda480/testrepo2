pipeline {
    agent {
        label 'rbhe'
    }
    stages {
        stage('SDLE Upload') {
            steps {
                script {
                    def projects = readYaml file: 'sdle.yaml'
                    def parallelStages = projects.collectEntries {
                        ["${it}" : generateStage(it)]
                    }
                    parallel parallelStages
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