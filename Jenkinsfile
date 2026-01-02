// -----------------------------
// Jenkinsfile (Scripted Pipeline)
// -----------------------------

// 1️⃣ Define job parameters
properties([
    parameters([
        string(
            name: 'PATH',defaultValue: 'default'
        )
    ])
])

node {

    def user = env.BUILD_USER_ID
    def path = params.PATH

    stage('Print parameters') {
        echo "Build triggered by user: ${user}"
        echo "PATH parameter: ${path}"
    }

    stage('Validate PATH parameter') {
        def pathRegex = '^/dev/files/operation systems/att/db/[^/]+/\\d{8}/[^/]+\\.zip$'

        if (!(path ==~ pathRegex)) {
            error """
            Invalid PATH parameter!

            Provided:
            ${path}

            Expected format:
            /dev/files/operation systems/att/db/<name>/<YYYYMMDD>/<file>.zip
            """
        }

        echo "PATH format is valid"
    }

    stage('Create JSON') {
        def targetDir = "${env.WORKSPACE}/output"
        sh "mkdir -p ${targetDir}"

        def jsonContent = """
        {
            "user": "${user}",
            "path": "${path}"
        }
        """

        writeFile file: "${targetDir}/first_job.json", text: jsonContent
        sh "cat ${targetDir}/first_job.json"
    }

    stage('Archive JSON') {
        archiveArtifacts artifacts: 'output/first_job.json', fingerprint: true
    }
}