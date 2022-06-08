def allowed_branches = [ "PROD" : ["main"], "QA" : ["integration"], "DEV" : [] ]

properties([
    parameters([
        gitParameter(branch: '', branchFilter: '.*', defaultValue: 'develop', description: 'Branch To Deploy, Any branch can be deployed on Dev', name: 'branch', quickFilterEnabled: true, selectedValue: 'NONE', sortMode: 'NONE', tagFilter: '*', type: 'PT_BRANCH_TAG'),
        choice(name: 'env', choices: ['DEV', 'QA', 'PROD'], description: 'Deployment Envrionment' )
    ])
])

node{
    stage('Initialize'){
        def gitRepo = checkout scm
        git_branch = gitRepo.GIT_BRANCH.tokenize('/')[-1]
        String git_short_commit_id = gitRepo.GIT_COMMIT[0..6]
        println("git_short_commit_id : "+git_short_commit_id)
        String buildTime = sh(returnStdout: true, script: "date +'%Y.%V'").trim()
        currentBuild.displayName = (buildTime + "." + currentBuild.number + "." + git_branch + "." + git_short_commit_id)
        def branch_list = allowed_branches[params.env]
        if (! branch_list.isEmpty() &&  !(git_branch in branch_list)) {
            error "Selected '${git_branch}' branch, is not allowed to be deployed on '${params.env}' environment."
        }
        echo "Deploying '${git_branch}' branch on ${params.env.toLowerCase()} environment."
    }
}
