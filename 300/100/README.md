# 100 - Deploy Node.js to Azure Web App

Based on "Deploy Node.js to Azure Web App" at [https://github.com/skills/hello-github-actions](https://github.com/vanHeemstraSystems/microsoft-azure-headstart/new/main?filename=.github%2Fworkflows%2Fazure-webapps-node.yml&workflow_template=deployments%2Fazure-webapps-node)

Based on "Simple NodeJS Application" at https://linuxhint.com/simple_nodejs_application/

The NodeJS app "Simple NodeJS Application" creates the following file at the root of our repository:

```
let http = require('http')
server = http.createServer(function(request, response) {
    response.write('Hello World')
    response.end()
})

server.listen(8080)
console.log ("Server Running")
```
**server.js**

Be sure to initiate the repository with ```npm init``` and install required NodeJS modules (e.g., http with ```npm install http```) which will be listed in the generated file ```package.json```.

```
{
  "name": "my_package",
  "description": "",
  "version": "1.0.0",
  "main": "server.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "repository": {
    "type": "git",
    "url": "https://github.com/vanHeemstraSystems/microsoft-azure-headstart.git"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "bugs": {
    "url": "https://github.com/vanHeemstraSystems/microsoft-azure-headstart/issues"
  },
  "homepage": "https://github.com/vanHeemstraSystems/microsoft-azure-headstart"
}
```
**package.json**

Make sure to at least once build your NodeJS application (with ```npm install```) so that a **package-lock.json** file gets created. Commit this file to the repository, as it is mandatory for the Github Actions workflow to exist.

```
... some text here

```
**package-lock.json**

___

This workflow assumes you have already created the target Azure App Service web app.

For instructions see https://docs.microsoft.com/en-us/azure/app-service/quickstart-nodejs?tabs=linux&pivots=development-environment-cli

To configure this workflow:

### Step 1:

Download the Publish Profile for your Azure Web App. You can download this file from the Overview page of your Web App in the Azure Portal.

For more information: https://docs.microsoft.com/en-us/azure/app-service/deploy-github-actions?tabs=applevel#generate-deployment-credentials

### Step 2:

Create a secret in your repository named AZURE_WEBAPP_PUBLISH_PROFILE, paste the publish profile contents as the value of the secret.

For instructions on obtaining the publish profile see: https://docs.microsoft.com/azure/app-service/deploy-github-actions#configure-the-github-secret

### Step 2:

Change the value for the AZURE_WEBAPP_NAME. Optionally, change the AZURE_WEBAPP_PACKAGE_PATH and NODE_VERSION environment variables below.
___

The workflow template "Deploy Node.js to Azure Web App" creates the following file at ```.github/workflows```:

```
# This workflow will build and push a node.js application to an Azure Web App when a commit is pushed to your default branch.
#
# This workflow assumes you have already created the target Azure App Service web app.
# For instructions see https://docs.microsoft.com/en-us/azure/app-service/quickstart-nodejs?tabs=linux&pivots=development-environment-cli
#
# To configure this workflow:
#
# 1. Download the Publish Profile for your Azure Web App. You can download this file from the Overview page of your Web App in the Azure Portal.
#    For more information: https://docs.microsoft.com/en-us/azure/app-service/deploy-github-actions?tabs=applevel#generate-deployment-credentials
#
# 2. Create a secret in your repository named AZURE_WEBAPP_PUBLISH_PROFILE, paste the publish profile contents as the value of the secret.
#    For instructions on obtaining the publish profile see: https://docs.microsoft.com/azure/app-service/deploy-github-actions#configure-the-github-secret
#
# 3. Change the value for the AZURE_WEBAPP_NAME. Optionally, change the AZURE_WEBAPP_PACKAGE_PATH and NODE_VERSION environment variables below.
#
# For more information on GitHub Actions for Azure: https://github.com/Azure/Actions
# For more information on the Azure Web Apps Deploy action: https://github.com/Azure/webapps-deploy
# For more samples to get started with GitHub Action workflows to deploy to Azure: https://github.com/Azure/actions-workflow-samples

on:
  push:
    branches: [ "main" ]
  workflow_dispatch:

env:
  AZURE_WEBAPP_NAME: your-app-name    # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  NODE_VERSION: '14.x'                # set this to the node version to use

permissions:
  contents: read

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3

    - name: Set up Node.js
      uses: actions/setup-node@v3
      with:
        node-version: ${{ env.NODE_VERSION }}
        cache: 'npm'

    - name: npm install, build, and test
      run: |
        npm install
        npm run build --if-present
        npm run test --if-present

    - name: Upload artifact for deployment job
      uses: actions/upload-artifact@v3
      with:
        name: node-app
        path: .

  deploy:
    permissions:
      contents: none
    runs-on: ubuntu-latest
    needs: build
    environment:
      name: 'Development'
      url: ${{ steps.deploy-to-webapp.outputs.webapp-url }}

    steps:
    - name: Download artifact from build job
      uses: actions/download-artifact@v3
      with:
        name: node-app

    - name: 'Deploy to Azure WebApp'
      id: deploy-to-webapp
      uses: azure/webapps-deploy@v2
      with:
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: ${{ env.AZURE_WEBAPP_PACKAGE_PATH }}

```
**azure-webapps-node.yml**
