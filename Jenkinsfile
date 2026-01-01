properties([
    parameters([
        string(name: 'PATH', defaultValue: 'default', description: 'Path value')
    ])
])

node {
    stage('Print parameters') {
        // Get the user who triggered the build
        def user = env.BUILD_USER_ID

        // Get PATH parameter
        def pathParam = params.PATH

        // Print to console
        echo "Build triggered by user: ${user}"
        echo "PATH parameter: ${pathParam}"
    }
}
