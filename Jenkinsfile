properties([
    parameters([
        string(name: 'PATH', defaultValue: 'default', description: 'Path value')
    ])
])

node {
    stage('Print parameters') {
        // Get the user who triggered the build
        def user = env.BUILD_USER_ID ?: "unknown"

        // Get PATH parameter
        def path = params.PATH

        // Print to console
        echo "Build triggered by user: ${user}"
        echo "PATH parameter: ${path}"
    }

    stage('Save parameters to JSON') {
        // Directory to store JSON
        def targetDir = '/opt/artifacts'
        def fileName = 'first_job.json'

        // Make sure the directory exists
        sh "mkdir -p ${targetDir}"

        // Build JSON content
        def jsonContent = """
        {
          "user": "${user}",
          "path": "${path}"
        }
        """

        // Write JSON file
        writeFile file: "${targetDir}/${fileName}", text: jsonContent

        // Optional: show content in console
        sh "cat ${targetDir}/${fileName}"
    }
}
