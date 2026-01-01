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

// 2️⃣ Start pipeline node
node {

    // -----------------------------
    stage('Print parameters') {
        // Get user who triggered the build (requires Build User Vars plugin)
        def user = env.BUILD_USER_ID ?: "unknown"

        // Get PATH parameter
        def path = params.PATH

        // Print to console
        echo "Build triggered by user: ${user}"
        echo "PATH parameter: ${path}"
    }

    // -----------------------------
    stage('Create JSON') {
        // Safe directory inside workspace
        def targetDir = "${env.WORKSPACE}/output"

        // Make sure the directory exists
        sh "mkdir -p ${targetDir}"

        // Build JSON content
        def jsonContent = """
        {
            "user": "${user ?: 'unknown'}",
            "path": "${path}"
        }
        """

        // Write JSON file
        writeFile file: "${targetDir}/first_job.json", text: jsonContent

        // Optional: print content to console
        sh "cat ${targetDir}/first_job.json"
    }

    // -----------------------------
    stage('Archive JSON') {
        // Make JSON available via Jenkins UI
        archiveArtifacts artifacts: 'output/first_job.json', fingerprint: true

        // Print a clickable download link
        echo "Download JSON here: ${env.BUILD_URL}artifact/output/first_job.json"
    }
}
