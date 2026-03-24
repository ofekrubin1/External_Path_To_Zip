// ----------------------------
// Jenkinsfile (Scripted Pipeline)
// ----------------------------

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
        def jsonContent, json_object
        def zipFile
        def destination = "linux2204-agent:/External_Path_To_Zip"

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
                jsonFile = "${build_number}_External_Path_To_Zip_${timestamp}.json"

                json_object = [
                  "user": user,
                  "path": path
                ]
                json_content = groovy.json.JsonOutput.toJson(json_object)
                println "json content is ${json_content}"


                writeFile file: jsonFile, text: json_content
                println "JSON file created at: ${env.WORKSPACE}/${json_file}"
            }

            stage('Create Zip') {
                zipFile = "${build_number}_External_Path_To_Zip_${timestamp}.zip"
                sh "zip -j ${zipFile} ${jsonFile}"
                sh "scp ${zipFile} ${destination}/${zipFile}"
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
            stage ('Clean Workspace') {
                println "🧹 Cleaning workspace..."
                cleanWs()
		        ws(pwd()+"@tmp"){
		        	cleanWs()
		        }
            }
        }
    }
}
