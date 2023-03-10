/*
    Downloads artifacts from a GitHub workflow and uploads them as evidence to a designated Project in SDLE.

    The following data structure contains a list of the projects to process, each will execute independently 
    in an isolated workspace and in parallel.

    The following metadata is required for each project:
        sdle_project_id : the id of project in sdle
        name: the name of project in sdle
        owner: the account owner of the GitHub repository
        repo: the name of the GitHub repository
        branch: the name of the branch within the repo that contains the workflow artifact to download
        artifacts: a comma-delimited string of artifact names to download
        folders: a comma-delimited string of folder names that the respective artifact should be downloaded to
*/
def projects = [
    [
        sdle_project_id: 15564,
        name: 'onboard-quickstart-samples',
        owner: 'intel-innersource',
        repo: 'applications.devops.responsive-retail.onboard-quickstart-samples',
        branch: 'us5090-gha',
        artifacts: 'Snyk Reports, Coverity Reports',
        folders: 'artifacts/Snyk, artifacts/Coverity'
    ], [
        sdle_project_id: 15564,
        name: 'onboard-quickstart-samples B',
        owner: 'intel-innersource',
        repo: 'applications.devops.responsive-retail.onboard-quickstart-samples',
        branch: 'us5090-gha',
        artifacts: 'BDBA Reports, Bandit Reports',
        folders: 'artifacts/Bdba, artifacts/Bandit'
    ], [
        sdle_project_id: 15564,
        name: 'onboard-quickstart-samples C',
        owner: 'intel-innersource',
        repo: 'applications.devops.responsive-retail.onboard-quickstart-samples',
        branch: 'us5090-gha',
        artifacts: 'Checkmarx Reports, Protex Reports',
        folders: 'artifacts/Checkmarx, artifacts/Protex'
    ], [
        sdle_project_id: 17178,
        name: 'RPS 2023',
        owner: 'open-amt-cloud-toolkit',
        repo: 'rps',
        branch: 'codeql_report',
        artifacts: 'report',
        folders: 'artifacts/CodeQL'
    ]
]

def parallelStages = projects.collectEntries {
    ["${it}" : generateStage(it)]
}

pipeline {
    agent {
        label 'rbhe'
    }
    stages {
        stage('SDLE Upload') {
            steps {
                script {
                    // parallel parallelStages
                    writeYaml file: 'sdle.yaml', data: projects
                    sh 'cat sdle.yaml'
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

                sdleUpload([
                   sdleUploadProjectId: project.sdle_project_id]) 
            }
        }
    }
}