// -----------------------------
// Jenkinsfile (Scripted Pipeline)
// -----------------------------

// Define job parameters
properties([
    parameters([
        string(
            name: 'MY_PATH',defaultValue: 'default'
        )
    ])
])

node('linux2204-agent') {

    timestamps {

        def user = env.BUILD_USER_ID
        def path = params.MY_PATH
        def build_number = env.BUILD_NUMBER
        def timestamp = new Date().format("yyyyMMdd_HHmmss", TimeZone.getTimeZone('GMT+2:00'))


        def pathRegex
        def jsonFile
        def jsonContent
        def zipFile

        stage('Print parameters') {
            println """
            PARAMETERS:
            Build triggered by user: ${user}
            MY_PATH parameter: ${path}
            Build number is: ${build_number}
            Timestamp is: ${timestamp}
            """
        }

        stage('Validate MY_PATH parameter') {
        try {
            pathRegex = /^\\\\dev\\files\\operation systems\\att\\db\\[^\\]+\\\d{8}\\[^\\]*zip[^\\]*$/

            if (!(path ==~ pathRegex)) {
                throw new Exception("MY_PATH format validation failed")
            }

            println "✅ MY_PATH format is valid"

        } catch (Exception e) {
            error """
            ❌ MY_PATH validation error

            Provided:
            ${path}

            Expected format:
            \\\\dev\\files\\operation systems\\att\\db\\<name>\\<YYYYMMDD>\\<file-containing-zip>

            Details:
            ${e.message}
            """
        }
        }
        stage('Create JSON') {
        
            // Relative to workspace – Jenkins creates it automatically
            jsonFile = build_number + 'first_job_' + timestamp + '.json'

            jsonContent = """
        {
          "user": "${user}",
          "path": "${path}"
        }
        """

            writeFile file: jsonFile, text: jsonContent

            println "JSON file created at: ${env.WORKSPACE}/${jsonFile}"
        }

        stage('Create Zip') {
            zipFile = build_number + 'first_job_' + timestamp + '.zip'
            sh "zip -j ${zipFile} ${jsonFile}"
            println "ZIP file created: ${zipFile}"
        }
        
        stage('Archive ZIP') {
        archiveArtifacts artifacts: zipFile, fingerprint: true
        }

        stage('Cleanup') {
            println "Cleaning workspace..."
            cleanWs()
        }
    }
}