**ASP.NET Web App Deployment Documentation**

**Introduction**
This documentation provides a step-by-step guide on how to create and deploy an ASP.NET web application using GitHub Actions for continuous integration and continuous delivery (CI/CD) to Azure App Service. This project aims to help me understand the process of building and deploying an ASP.NET web app using YAML-based CI/CD pipelines.

**Prerequisites**
Before starting the deployment process, ensure you have the following prerequisites:

Azure Subscription: You will need an active Azure subscription to create and configure Azure resources.
Azure App Service: Set up an Azure App Service instance where your web application will be deployed.
GitHub Account: Create a GitHub account to host your source code and configure GitHub Actions.
Visual Studio or Visual Studio Code: Install Visual Studio or Visual Studio Code to develop your ASP.NET web application.

Step 1: Create an ASP.NET Web Application
Launch Visual Studio or Visual Studio Code and create a new ASP.NET web application project.
Follow the prompts to select the desired ASP.NET project template and configure the project settings.
Develop your web application according to your requirements. For my project, I exclusively utilized the ASP.NET project template, 
with a primary focus on implementing a CI/CD pipeline.

Step 2: Set up Version Control with GitHub
Create a new repository on GitHub to host your source code.
Initialize your local ASP.NET project as a Git repository.
Connect your local Git repository to the GitHub remote repository.
Push your ASP.NET project code to the GitHub repository.

Step 3: Configure Azure App Service
Log in to the Azure Portal (portal.azure.com).
Create a new Azure App Service instance to deploy your web application.
Note down the App Service URL and connection details, as you'll need them in the deployment process.
Step 4: Configure Secrets in GitHub
In your GitHub repository, navigate to the "Settings" tab.
Click on "Secrets" in the left sidebar.
Click on "New repository secret" and create a secret named AZURE_APP_SERVICE_PUBLISH_PROFILE.
Paste the contents of your Azure App Service publish profile XML file into the value field of the secret.
To obtain the publish profile XML, go to your Azure App Service in the Azure Portal, navigate to "Deployment Center," and download the publish profile.
Open the XML file and copy its contents into the secret value field.
Click "Add secret" to save the secret.

Step 5: Create a GitHub Actions Workflow
In your GitHub repository, navigate to the "Actions" tab.
Click on the "Set up a workflow yourself" or "New workflow" button.
Create a new YAML file (e.g., deploy.yml) and copy the following sample:

    name: Build and deploy ASP.Net Core app to Azure Web App - MyFirstAzureWebApp20230528234401

    on:
      push:
        branches:
          - main
      workflow_dispatch:

    jobs:
      build:
        runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v2

      - name: Set up .NET Core
        uses: actions/setup-dotnet@v1
        with:
          dotnet-version: '7.x'
          include-prerelease: true

      - name: Build with dotnet
        run: dotnet build --configuration Release

      - name: dotnet publish
        run: dotnet publish -c Release -o ${{env.DOTNET_ROOT}}/myapp

      - name: Upload artifact for deployment job
        uses: actions/upload-artifact@v2
        with:
          name: .net-app
          path: ${{env.DOTNET_ROOT}}/myapp

     deploy:
        runs-on: ubuntu-latest
        needs: build
      environment:
      name: 'Production'
      url: ${{ steps.deploy-to-webapp.outputs.webapp-url }}

    steps:
      - name: Download artifact from build job
        uses: actions/download-artifact@v2
        with:
          name: .net-app

      - name: Deploy to Azure Web App
        id: deploy-to-webapp
        uses: azure/webapps-deploy@v2
        with:
          app-name: 'MyFirstAzureWebApp20230528234401'
          slot-name: 'Production'
          publish-profile: ${{ secrets.AZUREAPPSERVICE_PUBLISHPROFILE_824D782AA36D44BEA6A12AEED4F6A633 }}
          package: .
Save the deploy.yml file.
  
Step 6: Trigger the CI/CD Pipeline
Make any necessary changes or enhancements to your ASP.NET web application code.
Commit and push your changes to the GitHub repository's main branch.
Go to the "Actions" tab in your GitHub repository and verify that the CI/CD pipeline is triggered.
Monitor the pipeline execution for any errors or issues.
Once the pipeline completes successfully, your ASP.NET web application will be deployed to Azure App Service.

**Conclusion**
This workflow is triggered whenever a push event occurs on the main branch of your repository, 
initiating the CI/CD process for your ASP.NET web app. It performs the following steps:

1- A push event occurs on the main branch of the GitHub repository.
2- The GitHub Actions workflow is triggered.
3- The repository is checked out to retrieve the latest source code.
4- The .NET environment is set up with the desired version.
5- The ASP.NET web app is built and published, generating deployment artifacts.
6- The generated artifacts are deployed to Azure App Service.
7- The web app is hosted and made accessible via Azure App Service.
  
Here's a diagram illustrating the steps involved in the ASP.NET web app deployment process using GitHub Actions:
                     +----------------------------------+
                   |                                  |
                   |     GitHub Repository            |
                   |                                  |
                   +----------+-----------------------+
                              |
                     Push Event (main branch)
                              |
                              v
                   +----------+-----------------------+
                   |                                  |
                   |     GitHub Actions Workflow      |
                   |                                  |
                   +----------+-----------------------+
                              |
                   Checkout repository
                              |
                   Setup .NET environment
                              |
                   Build and publish web app
                              |
                   Deploy to Azure App Service
                              |
                   +----------+-----------------------+
                   |                                  |
                   |     Azure App Service            |
                   |                                  |
                   +----------------------------------+
