# Auto-Pilot Mode: AI Assistance in Software Development
This lab exercise demonstrates integrating GitHub Copilot into .NET application development to implement health checks, automate CI/CD pipelines for Azure deployment, and explore AI's role in enhancing software development efficiency through practical programming tasks.

## Prerequisites
- The prerequisites steps must be completed, see [Labs Prerequisites](./Labs/Lab%201.1%20-%20Pre-Flight%20Checklist)

## Estimated time to complete
- 15 min

## Objectives
- Demonstrate GitHub Copilot's capabilities in .NET development, focusing on automating health checks, CI/CD pipelines for Azure, infrastructure as code, and generating concise "Summarized Check in Comments" and "Summarized Pull Request Comments".
- Highlight Copilot's impact on streamlining development workflows, improving code quality, and enhancing collaboration through practical tasks.

### Step 1. Fasten your seatbelts, turbulence incoming - Committing Code Changes

- Open `Program.cs` in `WrightBrothersApi` folder

- Note the following code that adds a health check to the application. The healthcheck simulates a health check that sometimes is healthy, sometimes is degraded, and sometimes is unhealthy.

    ```csharp
    // Other code

    var builder = WebApplication.CreateBuilder(args);

    builder.Services.AddHealthChecks()
    .AddCheck("CruisingAltitudeCheck", () =>
    {
        bool atCruisingAltitude = CheckSystemPerformance(); 

        if (atCruisingAltitude)

        {
            return HealthCheckResult.Healthy("The application is cruising smoothly at optimal altitude.");
        }
        else
        {
            bool minorIssue = CheckIfMinorIssue();

            return minorIssue ?
                HealthCheckResult.Degraded("The application is experiencing turbulence but remains stable.") :
                HealthCheckResult.Unhealthy("The application is facing a system failure and needs immediate attention.");
        }

        bool CheckSystemPerformance()
        {
            // Simulate a check to determine if the application is "at cruising altitude"
            // For the sake of this example, we'll just return a random value
            Random random = new Random();
            int randomNumber = random.Next(1, 100);

            return randomNumber > 10;
        }

        bool CheckIfMinorIssue()
        {
            // Simulate a check to determine if the application is "at cruising altitude"
            // For the sake of this example, we'll just return a random value
            Random random = new Random();
            int randomNumber = random.Next(1, 100);

            return randomNumber > 50;
        }
    });

    // Other code
    ```

- Run the application to see the health check in action.

    ```sh
    cd WrightBrothersApi
    dotnet run
    ```

>[!Note]
> If you encounter an error message like `Project file does not exist.` or `Couldn't find a project to run.`, it's likely that you're executing the command from an incorrect directory. To resolve this, navigate to the correct directory using the command `cd ./WrightBrothersApi`. If you need to move one level up in the directory structure, use the command `cd ..`. The corrcect directory is the one that contains the `WrightBrothersApi.csproj` file.

- Open the `Examples/Healthcheck.http` file, click `Send Request` to execute the `health` request.

<img src="../../Images/Screenshot-Http-Healthcheck.png" width="600">

> [!Note]
> Screenshot is made at 8th of February 2024. The UI of the Copilot Chat extension can be different at the time you are doing the lab. (Please notify us if the UI is different.)

- Click the `Send Request` button for the `GET` below:

    ```json
    GET http://localhost:1903/health HTTP/1.1
    ```

- The response should be `200 OK` with the following body:

    ```json
    {
        "status": "Healthy", // or "Degraded" or "Unhealthy"
        "totalDuration": "00:00:00.0000001"
    }
    ```

- Stop the application by pressing `Ctrl+C` in the terminal.

- Let's make a small change to the code. Change the `CheckSystemPerformance` method to return a random number greater than 50. This will simulate a more unstable system.

    ```csharp
    bool CheckSystemPerformance()
    {
        // Simulate a check to determine if the application is "at cruising altitude"
        // For the sake of this example, we'll just return a random value
        Random random = new Random();
        int randomNumber = random.Next(1, 100);

        return randomNumber > 50;
    }
    ```

- Create a new feature branch `feature/health-checks` from the `main` branch in your terminal

```sh
git checkout -b feature/health-checks
```

- Open the Source Control tab in VS Code

- In the `Changes` area, click the `+` icon to `Stage all changes`

- Click on the `Magic` icon to generate a commit message

<img src="../../Images/Screenshot-Healthcheck-commit.png" width="600">


> [!Note]
> GitHub Copilot Chat suggests a commit message based on the changes made to the code. This is a great way to get started with a commit message.


- Click the `✓ Commit & Sync` button to commit the changes.

<img src="../../Images/Screenshot-commit-sync.png" width="400">

- Click the `Publish branch` button to push the changes.

<img src="../../Images/Screenshot145014.png" width="500">


## GitHub Copilot Enterprise Only

### Step 2. Turn on Autopilot Mode - Automating GitHub Pull Requests

> [!WARNING]  
> You must complete the previous lab before continuing.

> [!Note]
> Pull Request summaries in the GitHub.com portal is not yet supported for everyone. The trainer will demo this.

- Go to your `GitHub.com` repository.

- Click on the `Pull requests` tab.

- Click on the `New pull request` button

- Select the `main` branch for the base branch.
- Select the `feature/health-checks` branch as the compare branch

<img src="../../Images/Screenshot145342.png" width="500">

- Click the `Create pull request` button

- Click on the `Copilot` icon, select `Summary` to generate a summary of changes in this pull request.

<img src="../../Images/Screenshot171550.png" width="800">

- Click `Preview` to see the summary.

<img src="../../Images/Screenshot160132.png" width="800">

- Click `Create pull request` to create the pull request.

> [!IMPORTANT]  
> This is a `Copilot Enterprise` feature only! In order to use the Pull Request Summaries feature you need a Copilot Enterprise License and have this feature enabled in your GitHub account.

## Optional

### Step 3. Smooth Flying in the Cloud - Automating GitHub Pipelines
A build pipeline automates your software's build, test, and deployment processes, ensuring consistent and error-free releases while saving time and improving code quality. It streamlines development, enables quick feedback, and supports efficient version management.  Let's begin by
automating CI/CD pipelines for deployment to Azure.

- Open the GitHub Copilot Chat extension

- Type the following command

    ```
    @workspace create a build pipeline for the application
    ```

- GitHub Copilot Chat will suggest creating a GitHub Actions build pipeline for the application. It also includes a build step and a test step.

    ```yaml
    name: .NET Core CI

    on:
    push:
        branches: [ main ]
    pull_request:
        branches: [ main ]

    jobs:
    build:

        runs-on: ubuntu-latest

        steps:
        - uses: actions/checkout@v2

        - name: Setup .NET
        uses: actions/setup-dotnet@v1
        with:
            dotnet-version: 5.0.x

        - name: Restore dependencies
        run: dotnet restore "WrightBrothersApi/WrightBrothersApi.csproj"

        - name: Build
        run: dotnet build "WrightBrothersApi/WrightBrothersApi.csproj" --configuration Release --no-restore

        - name: Test
        run: dotnet test "WrightBrothersApi.Tests/WrightBrothersApi.Tests.csproj" --no-restore --verbosity normal
    ```

- In the Copilot Chat extension window, click the ellipses `...` and select `Insert into New File` for the suggested pipeline.
- Copilot will add the code to a new empty file, but must be saved.
- Save the file by clicking pressing `Ctrl + S` or `Cmd + S`.
- Change directory to the `.github/workflows` folder`.
- Enter the file name `Build.yml` and click `Save`.


> [!Note]
> With the @workspace agent, GitHub Copilot understands that the current workspace is a .NET application with a Test project in it. It also understands that the application is hosted in a folder called `WrightBrothersApi` and the test project is in a folder called `WrightBrothersApi.Tests`. This is a great example of how GitHub Copilot can understand the context of the current workspace and provide suggestions based on that context.

## Optional

### Step 4. Ground Control - How to assent to the Azure Cloud
Deploying your application to Azure facilitates scalable, secure, and efficient hosting, leveraging Microsoft's cloud infrastructure. This allows for easy scaling, robust disaster recovery, and global reach, enhancing your app's performance and accessibility while minimizing maintenance efforts and costs.

> [!WARNING]  
> You must complete the previous lab before continuing.

- Pre-requisite is a valid `*.yml` build pipeline from previous step.

- Select all the content of the `Build.yml`.
- In the Copilot Chat extension window, type the following command.

    ```
    Deploy to Azure
    ```

<img src="../../Images/Screenshot-deploy-azure.png" width="500">

- GitHub Copilot Chat will suggest adding a deploy step to the pipeline, which is a Azure Web App deployment.

    ```yaml
    # Rest of the pipeline

    deploy:
        needs: build
        runs-on: ubuntu-latest
        steps:
        - name: Login to Azure
            uses: azure/login@v1
            with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}

        - name: Deploy to Azure Web App
            uses: azure/webapps-deploy@v2
            with:
            app-name: 'your-app-name' # replace with your app name
            publish-profile: ${{ secrets.AZURE_PUBLISH_PROFILE }}
            package: './WrightBrothersApi'
    ```

- In the Copilot Chat extension window, click the ellipses `...` and select `Insert into New File` for the suggested pipeline.
- Copilot will add the code to a new empty file, but must be saved.
- Save the file by clicking pressing `Ctrl + S` or `Cmd + S`.
- Change directory to the `.github/workflows` folder`.
- Enter the file name `Deploy.yml` and click `Save`.

- Start a new chat with GitHub Copilot Chat and type the following command.

    ```
    @workspace create the infrastructure as Code files using Bicep that I need for a Web App in Azure
    ```

- In the Copilot Chat extension window, click the ellipses `...` and select `Insert into New File` for the suggested pipeline.
- Copilot will add the code to a new empty file, but must be saved.
- Save the file by clicking pressing `Ctrl + S` or `Cmd + S`.
- Change directory to the `WrightBrothersAPI` root folder`.
- Enter the file name `Main.bicep` and click `Save`.


## Optional

### Step 5. Flight Plan - Crafting a Detailed DevOps Pipeline with Bicep IaC
Using Infrastructure as Code (IaC) to manage your cloud resources, specifically with Bicep for Azure environments, offers several significant advantages that make it an essential practice for modern cloud infrastructure management. 

> [!Note]
> GitHub Copilot's advanced prompt is a powerful feature that allows you to ask complex questions and receive detailed responses. It can help you plan and execute complex tasks, ensuring that all steps are clearly defined and logically sequenced for efficient execution.

- Open the Copilot Chat extension and ask the following advanced `Chain of Thought` prompt engineered question:

    ```
    Create a single DevOps pipeline comprising three stages: Build, Infrastructure as Code (IaC), and Quality Assurance (QA). 

    - **Task 1: Build Stage**
    - Describe the application and its architecture.
    - List the programming languages and frameworks used.
    - Outline the steps needed to compile and build the application.
    
    - **Task 2: IaC (Infrastructure as Code) Stage**
    - Explain what Bicep is and why it's chosen for this project.
    - Specify the Azure resources required for the application (e.g., App Service, Azure SQL Database).
    - Request the creation of Bicep templates for these resources, highlighting any dependencies between them.

    - **Task 3: QA (Quality Assurance) Stage**
    - Describe the deployment environment in Azure.
    - List the tests that need to be performed (e.g., unit tests, integration tests, UI tests).
    - Detail the steps for deploying the application to Azure for QA purposes.
    
    Please create a comprehensive plan for each stage, ensuring that all steps are clearly defined and logically sequenced for efficient execution.

    Let's think step by step.
    ```

> [!Note]
> This prompt adds clarity by breaking down each stage into more detailed tasks and requesting specific outcomes. This method encourages Copilot to follow a logical sequence of thoughts and generate outputs that closely align with your requirements. By providing more context and detail, you increase the likelihood of receiving a comprehensive and actionable plan from GitHub Copilot.

To refine the Chain of Thought prompt further, incorporating explicit instructions for GitHub Copilot to scaffold out each required file, let's adjust the prompt to specify the creation of file outlines or templates. This approach will help in generating a more structured and practical output that includes the scaffolding for each stage of the project.

- Open the Copilot Chat extension and ask the following advanced `Chain of Thought` prompt engineered question:

    ```
    Create a single DevOps pipeline comprising three stages: Build, Infrastructure as Code (IaC), and Quality Assurance (QA). 

    - **Task 1: Build Stage**
    - Describe the application and its architecture.
    - List the programming languages and frameworks used.
    - Outline the steps needed to compile and build the application.
    
    - **Task 2: IaC (Infrastructure as Code) Stage**
    - Explain what Bicep is and why it's chosen for this project.
    - Specify the Azure resources required for the application (e.g., App Service, Azure SQL Database).
    - Request the creation of Bicep templates for these resources, highlighting any dependencies between them.

    - **Task 3: QA (Quality Assurance) Stage**
    - Describe the deployment environment in Azure.
    - List the tests that need to be performed (e.g., unit tests, integration tests, UI tests).
    - Detail the steps for deploying the application to Azure for QA purposes.
    
    Please create a comprehensive plan for each stage, ensuring that all steps are clearly defined and logically sequenced for efficient execution.

    Let's think step by step.
    ```

> [!Note]
> This revised prompt explicitly requests the scaffolding of files for each stage of the development and deployment process. It guides GitHub Copilot to not only describe the steps involved but also to create the basic structure of each required file, providing a clear starting point for development. By doing so, you're asking for both a plan and the initial implementation files, streamlining the setup process for your project.

For a scenario focusing on a .NET 7 Web API application and requiring the execution of Bicep files for Infrastructure as Code (IaC) deployment, the prompt should guide the AI to scaffold the project with specific attention to .NET 7, Azure resources, and the execution hierarchy of Bicep files.

- Open the Copilot Chat extension and ask the following advanced `Chain of Thought` prompt engineered question:

    ```
    Let's set up a CI/CD pipeline for a .NET 7 Web API application, which includes stages for Build, Infrastructure as Code (IaC) deployment using Bicep, and Quality Assurance (QA). Each stage should be clearly defined, and necessary files and configurations must be scaffolded out.

    1. **Build Stage for .NET 7 Web API**:
    - The application is built with .NET 7, utilizing C# as the programming language. Scaffold the build process, which should include steps for restoring NuGet packages, compiling the solution, running unit tests, and publishing the output. Create a 'build.yml' file that outlines these steps for the CI pipeline.

    2. **IaC Stage with Bicep Execution**:
    - For deployment to Azure, identify the required Azure resources (e.g., Azure App Service for hosting the Web API, Azure SQL Database for data storage). Scaffold Bicep templates for these resources, ensuring they're modular and reusable.
    - Create a 'main.bicep' file that acts as the entry point, invoking other Bicep files (e.g., 'appservice.bicep', 'sqldatabase.bicep') to define the entire infrastructure. Include a step in the CI/CD pipeline ('iac-deploy.yaml') to authenticate with Azure and execute 'main.bicep', deploying the infrastructure.

    3. **QA Stage**:
    - Detail the process for deploying the .NET 7 Web API to the provisioned Azure App Service in a QA environment. This includes applying database migrations if necessary and running integration and acceptance tests.
    - Scaffold a 'qa-deploy.yml' file that outlines the steps for deploying the application to Azure, setting up the environment, and executing tests.

    In addition to scaffolding these files, provide explanations for each file's purpose and the critical sections within them. This approach ensures a comprehensive understanding and correct implementation of the pipeline for deploying a .NET 7 Web API application to Azure."
    ```

> [!Note]
> This prompt is structured to ensure that GitHub Copilot generates a detailed plan, breaking down the requirements for a .NET 7 Web API application's CI/CD pipeline, including specific focus on IaC with Bicep for Azure deployments. By explicitly asking for a 'main.bicep' to orchestrate the deployment of other Bicep modules, it directs the AI towards creating a scalable and maintainable infrastructure setup.

<details>
  <summary>Click to expand Solution</summary>

    #### Build Stage for .NET 7 Web API:
    ```
    # build.yaml
    trigger:
    - main

    pool:
    vmImage: 'windows-latest'

    variables:
    buildConfiguration: 'Release'

    steps:
    - task: NuGetToolInstaller@1

    - task: NuGetCommand@2
    inputs:
        restoreSolution: '**/*.sln'

    - task: VSBuild@1
    inputs:
        solution: '**/*.sln'
        msbuildArgs: '/p:DeployOnBuild=true /p:WebPublishMethod=Package /p:PackageAsSingleFile=true /p:SkipInvalidConfigurations=true /p:DesktopBuildPackageLocation="$(build.artifactStagingDirectory)\WebApp.zip" /p:DeployIisAppPath="Default Web Site"'
        platform: '$(BuildPlatform)'
        configuration: '$(BuildConfiguration)'

    - task: VSTest@2
    inputs:
        platform: '$(BuildPlatform)'
        configuration: '$(BuildConfiguration)'

    - task: PublishBuildArtifacts@1
    ```

    #### IaC Stage with Bicep Execution
    ```
    // main.bicep
    targetScope = 'resourceGroup'

    module appService './appservice.bicep' = {
    name: 'appServiceModule'
    params: {
        name: 'myAppService'
    }
    }

    module sqlDatabase './sqldatabase.bicep' = {
    name: 'sqlDatabaseModule'
    params: {
        name: 'mySqlDatabase'
    }
    }

    # iac-deploy.yaml
    trigger:
    - main

    pool:
    vmImage: 'windows-latest'

    steps:
    - task: AzureCLI@2
    inputs:
        azureSubscription: '<Azure-Subscription-Name>'
        scriptType: 'bash'
        scriptLocation: 'inlineScript'
        inlineScript: 'az deployment group create --resource-group <Resource-Group-Name> --template-file ./main.bicep'
        
    ```

    #### QA Stage
    ```
    # qa-deploy.yaml
    trigger:
    - main

    pool:
    vmImage: 'windows-latest'

    steps:
    - task: AzureRmWebAppDeployment@4
    inputs:
        ConnectionType: 'AzureRM'
        azureSubscription: '<Azure-Subscription-Name>'
        appType: 'webApp'
        WebAppName: '<Web-App-Name>'
        packageForLinux: '$(Build.ArtifactStagingDirectory)/**/*.zip'

    - task: AzureCLI@2
    inputs:
        azureSubscription: '<Azure-Subscription-Name>'
        scriptType: 'bash'
        scriptLocation: 'inlineScript'
        inlineScript: 'az webapp config appsettings set --name <Web-App-Name> --resource-group <Resource-Group-Name> --settings "ASPNETCORE_ENVIRONMENT=QA"'

    - task: VSTest@2
    inputs:
        testAssemblyVer2: '**\*test*.dll'
        platform: '$(BuildPlatform)'
        configuration: '$(BuildConfiguration)
    ```
</details>
<br>

The evolution of the Chain of Thought (CoT) techniques across our conversation began with crafting a directive to create a single pipeline with stages for Build, IaC using Bicep, and QA for cloud resources. The initial CoT prompt lacked explicit steps and reasoning for each stage, focusing more on what to do rather than how and why to do it.

We then refined the approach to break down the task into more detailed steps, adding explanations and context to guide GitHub Copilot more effectively. This included describing the application, specifying resources, and detailing actions for each stage but still didn't fully integrate the execution of Bicep files.

Finally, we further adjusted the prompt for a .NET 7 Web API application, explicitly including the execution of Bicep files through a main.bicep file. This approach provided a detailed, step-by-step guide that not only outlined what needed to be done but also how to do it, specifically tailoring the tasks for .NET 7 and ensuring a modular and executable IaC setup.

> [!Note]
> By following this iterative process of refining the CoT prompt, you can effectively guide GitHub Copilot to generate more accurate and actionable outputs that align with your project requirements. This method helps in leveraging the AI's capabilities to scaffold out the necessary files and configurations, streamlining the development and deployment process for your cloud-based applications.

### Congratulations you've made it to the end! &#9992; &#9992; &#9992;

#### And with that, you've now concluded this module. We hope you enjoyed it! &#x1F60A;

> [!Note]
> ~~GitHub Copilot's /new command is a special command that scaffolds a new project can be a powerful ally in generating Infrastructure as Code. It enhances learning, increases efficiency, reduces errors, and helps maintain high-quality, up-to-date code~~
~~<img src="../../Images/Screenshot-azure-bicep.png" width="300">~~