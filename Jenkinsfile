// -----------------------------
// Jenkinsfile (Scripted Pipeline)
// -----------------------------

properties([
    parameters([
        string(name: 'MY_PATH', defaultValue: 'default')
    ])
])

node('linux2204-agent') {

    timestamps {

        def user = env.BUILD_USER_ID
        def path = params.MY_PATH
        def build_number = env.BUILD_NUMBER
        def timestamp = new Date().format("yyyyMMdd_HHmmss", TimeZone.getTimeZone('GMT+2'))

        def pathRegex
        def jsonFile
        def jsonContent
        def zipFile

        try {

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
                pathRegex = /^\\\\dev\\files\\operation systems\\att\\db\\[^\\]+\\\d{8}\\[^\\]*.zip[^\\]*$/

                if (!(path ==~ pathRegex)) {
                    error """
                ❌ MY_PATH validation error
                Provided:
                ${path}

                Expected format:
                \\\\dev\\files\\operation systems\\att\\db\\<name>\\<YYYYMMDD>\\<file-containing-zip>
                """
                }
                println "✅ MY_PATH format is valid"
            }

            stage('Create JSON') {
                jsonFile = "${build_number}_first_job_${timestamp}.json"

                jsonContent = """
                {
                  "user": "${user}",
                  "path": "${path}"
                }
                """
                writeFile file: jsonFile, text: jsonContent
                println "JSON file created: ${jsonFile}"
            }

            stage('Create Zip') {
                zipFile = "${build_number}_first_job_${timestamp}.zip"
                sh "zip -j ${zipFile} ${jsonFile}"
                sh "scp ${zipFile} 192.168.1.120:~/first_job/${zipFile}"
                println "ZIP file created: ${zipFile}"
            }

            stage('Archive ZIP') {
                archiveArtifacts artifacts: zipFile, fingerprint: true
            }

        } catch (Exception e) {

            currentBuild.result = 'FAILURE'
            println "❌ Build failed: ${e.message}"
            throw e

        } finally {
            println "🧹 Cleaning workspace..."
            cleanWs(deleteDirs: true)
        }
    }
}
