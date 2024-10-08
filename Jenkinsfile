node('test-app-server-02')
{

def app
stage('Cloning Git')
{
    /* Let's make sure we have the repository cloned to our workspace */
    checkout scm
}

stage('Build-and-Tag')
{
    /* This builds the actual image; 
         * This is synonymous to docker build on the command line */
    app = docker.build('amalan06/car_docker_repo')
}

stage('Post-to-dockerhub')
{
    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub_credentials')
    {
        // Push the built image to Docker Hub
            app.push('latest')  // You can specify a tag here if needed
    }
}

stage('Deploy') {
    steps {
        script {
            // Check if docker-compose is installed
            def composeExists = sh(script: 'command -v docker-compose', returnStatus: true) == 0

            // Install docker-compose if it doesn't exist
            if (!composeExists) {
                echo "docker-compose not found. Installing..."
                sh """
                   sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
                   sudo chmod +x /usr/local/bin/docker-compose
                   sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
                """
                echo "docker-compose installed successfully."
            } else {
                echo "docker-compose already installed."
            }

            // Proceed with the deployment
            sh "docker-compose down"
            sh "docker-compose up -d"
        }
    }
}


}
