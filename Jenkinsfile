// -----------------------------
// Jenkinsfile (Scripted Pipeline)
// -----------------------------

// 1️⃣ Define job parameters
properties([
    parameters([
        string(
            name: 'PATH',
            defaultValue: 'default',
            description: 'Path value'
        )
    ])
])

node { // Must be inside node for shell and workspace

    stage('Print parameters') {
        // Get Jenkins user who triggered the build
        def user = env.BUILD_USER_ID ?: "unknown"

        // Get PATH parameter
        def path = params.PATH

        // Print to console
        echo "Build triggered by user: ${user}"
        echo "PATH parameter: ${path}"
    }

    stage('Create JSON') {
        def targetDir = "${env.WORKSPACE}/output"

        // Create folder inside workspace
        sh "mkdir -p ${targetDir}"

        // Build JSON content
        def jsonContent = """
        {
            "user": "${user}",
            "path": "${path}"
        }
        """

        // Write JSON file
        writeFile file: "${targetDir}/first_job.json", text: jsonContent

        // Print content for debugging
        sh "cat ${targetDir}/first_job.json"
    }

    stage('Archive JSON') {
        // Archive to Jenkins UI
        archiveArtifacts artifacts: 'output/first_job.json', fingerprint: true

        echo "Download JSON here: ${env.BUILD_URL}artifact/output/first_job.json"
    }
}
