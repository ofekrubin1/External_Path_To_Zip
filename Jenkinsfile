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

        def pathRegex
        def jsonFile
        def jsonContent

        stage('Print parameters') {
            echo "Build triggered by user: ${user}"
            echo "MY_PATH parameter: ${path}"
            echo "Build number is: ${build_number}"
        }

        stage('Validate MY_PATH parameter') {
        try {
            pathRegex = /^\/\/dev\/files\/operation systems\/att\/db\/[^\/]+\/\d{8}\/[^\/]*zip[^\/]*$/

            if (!(path ==~ pathRegex)) {
                throw new Exception("MY_PATH format validation failed")
            }

            echo "✅ MY_PATH format is valid"

        } catch (Exception e) {
            error """
            ❌ MY_PATH validation error

            Provided:
            ${path}

            Expected format:
            //dev/files/operation systems/att/db/<name>/<YYYYMMDD>/<file-containing-zip>

            Details:
            ${e.message}
            """
        }
        }
        stage('Create JSON') {
        
            // Relative to workspace – Jenkins creates it automatically
            jsonFile = 'first_job_' + build_number + '.json'

            jsonContent = """
        {
          "user": "${user}",
          "path": "${path}"
        }
        """

            writeFile file: jsonFile, text: jsonContent

            echo "JSON file created at: ${env.WORKSPACE}/${jsonFile}"
        }

        //Take the file from workspace
        //Copies it into Jenkins’ build record, 
        //Stores it under that specific build and show it in the UI (even if the workspace is later cleaned)
        stage('Archive JSON') {
        archiveArtifacts artifacts: jsonFile, fingerprint: true
        }

        //stage('Copy to local PC') {
        //sh """
        //  scp first_job.json root@192.168.1.120:/opt/artifacts
        //"""
        //}
    }
}