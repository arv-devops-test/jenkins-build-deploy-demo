import groovy.json.JsonSlurper

node {
    
    echo "Workspace=${WORKSPACE}"
    echo "User="
    ExecuteShellCommand("whoami", false)
    def dockerRepoServerName
    def dockerImageName
    def AzClientId
    def AzClientSecret
    def AzTenantId
    def AzSubId
    

    withCredentials([azureServicePrincipal('63d6d38b-38c7-4a70-8ca9-cd82e40da5d7')]) {
        AzClientId = AZURE_CLIENT_ID
        AzClientSecret = AZURE_CLIENT_SECRET
        AzTenantId = AZURE_TENANT_ID
        AzSubId = AZURE_SUBSCRIPTION_ID
        
        
        
        /*echo "Azure CLient ID={$AZURE_CLIENT_ID}"
        echo "Azure CLient Secret={$AZURE_CLIENT_SECRET}"
        echo "Azure Tenant ID={$AZURE_TENANT_ID}"
        echo "Azure Subscription ID={$AZURE_SUBSCRIPTION_ID}"
        */
        stage('Prepare Environment') {
            sh 'az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET -t $AZURE_TENANT_ID'
            sh 'az account set -s $AZURE_SUBSCRIPTION_ID'
            settings = sh(script: "az acr show --resource-group arvin-docker-registry-resource --name arvindockerregistry", returnStdout: true)
            //echo settings
            //def arcSettings = new JsonSlurper().parseText(settings)
            dockerRepoServerName = new JsonSlurper().parseText(settings).loginServer
            dockerImageName = dockerRepoServerName + "/my-app:${env.BUILD_ID}"
            
            //sh "docker login -u ${AZURE_CLIENT_ID} -p ${AZURE_CLIENT_SECRET} ${dockerRepoServerName}"
            //def container = docker.build("${arcSettings.loginServer}/my-app:${env.BUILD_ID}")
        }
        
        stage('Pre-build Cleanup') {
            CleanWorkspace()
        }
    
        stage('Get-Git-Repo') {
            git url: 'https://github.com/arv-devops-test/jws31-tomcat8-jdk7.git'
        }
        
        stage('Build and Push Docker Image') {
            def container = BuildDockerImage(dockerImageName)
            PushDockerImage(container, AzClientId, AzClientSecret, dockerRepoServerName)
            //container = docker.build("${arcSettings.loginServer}/my-app:${env.BUILD_ID}")
        }
    }
    
    

    /*
    stage('Publish Docker Image') {
        ExecuteShellCommand("docker login -u ${AZURE_CLIENT_ID} -p ${AZURE_CLIENT_SECRET} ${acrSettings.loginServer}", true)
        container.push()
        ExecuteShellCommand("docker image prune -f", true)
    }
     
    
    stage('Post-build Cleanup') {
        
        ExecuteShellCommand("docker image prune -f", false)
        CleanWorkspace()
    }
    
    */
}

def BuildDockerImage(imageName)
{
    return docker.build(imageName)
}

def PushDockerImage(dockerImage, clientId, clientSecret, dockerRepoServerName)
{
    withCredentials([usernamePassword(credentialsId: 'DockerPrivateRegistryId', passwordVariable: 'PASSWORD', usernameVariable: 'USERNAME')]) {

        ExecuteShellCommand("docker login -u ${USERNAME} -p ${PASSWORD} ${dockerRepoServerName}", true)
        dockerImage.push()
    }
    //ExecuteShellCommand("docker login -u ${clientId} -p ${clientSecret} ${dockerRepoServerName}", true)

}


def CleanWorkspace()
{
    ws_content = sh (
                    script: "ls",
                    returnStdout: true
                    ).trim()
        echo ws_content
        
        if(ws_content != "")
        {
            echo "Workspace has content:"
            echo "Cleaning workspace..."
            sh 'rm -r $WORKSPACE/* 2>/dev/null';
        }
}

def ExecuteShellCommand(cmd, failOnError) {
   def errorHandling = ""
   if(!failOnError)
   {
       errorHandling = "Ignoring Exit Code as specified"
   }
   try
   {
        exitCode = sh (script: cmd,
                      returnStatus: true)
        
        echo "Finished task with Exit Code: ${exitCode.toString()}  :: ${errorHandling}"
        if(exitCode != 0 && failOnError)
        {
            error("An error occured while executing this: ${cmd}")
        }
   }
   
   catch(ex)
   {
        if(failOnError)
        {
            error("An error occured while executing this: ${cmd}")
        }
   }
   
}
