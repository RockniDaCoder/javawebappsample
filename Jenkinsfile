node {
  withEnv([
    'AZURE_SUBSCRIPTION_ID=dd436faa-a6c9-4a16-be32-4280baddeefb',
    'AZURE_TENANT_ID=ebae2e3d-83ed-4c17-8bb8-58fb2c72ba5a'
  ]) {
    stage('init') {
      checkout scm
    }

    stage('build') {
      sh 'mvn clean package'
    }

    stage('deploy') {
      def resourceGroup = 'jenkins-get-started-rg'
      def webAppName = 'zhuoyan-jenkins-app-2026'

      withCredentials([usernamePassword(
        credentialsId: 'AzureServicePrincipal',
        passwordVariable: 'AZURE_CLIENT_SECRET',
        usernameVariable: 'AZURE_CLIENT_ID'
      )]) {
        sh '''
          set +x
          az login --service-principal \
            --username "$AZURE_CLIENT_ID" \
            --password "$AZURE_CLIENT_SECRET" \
            --tenant "$AZURE_TENANT_ID" \
            --output none
          az account set --subscription "$AZURE_SUBSCRIPTION_ID"
        '''
      }

      try {
        sh "az webapp deploy --resource-group ${resourceGroup} --name ${webAppName} --src-path target/calculator-1.0.war --type war"
      } finally {
        sh 'az logout'
      }
    }
  }
}
