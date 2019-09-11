# Readme.md

These are notes I made as I went through the process of creating a build / deploy pipeline for an Azure Function written in Java.

Using this local folder: C:\repos\blizzDevOpsDemos\AzureFunctionsJava

Following along with: <https://docs.microsoft.com/en-us/azure/azure-functions/functions-create-first-java-maven>

Prereqs

* install JDK
* install maven
* a few other things -- see the link above

## In an empty folder, create a new Functions project

I'm using pwsh (PowerShell 6) in my VS Code terminal. This could be done from a CMD prompt, too.

    mvn archetype:generate `
        "-DarchetypeGroupId=com.microsoft.azure" `
        "-DarchetypeArtifactId=azure-functions-archetype"

Note - I started out using folder structure .\app2\fabrikam-functions, but later rearranged things and put the app in .\fabFunctionsApp

### Output

    PS C:\repos\blizzDevOpsDemos\AzureFunctionsJava\app2\fabrikam-functions> mvn archetype:generate `
    >>     "-DarchetypeGroupId=com.microsoft.azure" `
    >>     "-DarchetypeArtifactId=azure-functions-archetype"
    [INFO] Scanning for projects...
    [INFO]
    [INFO] ------------------< org.apache.maven:standalone-pom >-------------------
    [INFO] Building Maven Stub Project (No POM) 1
    [INFO] --------------------------------[ pom ]---------------------------------
    [INFO]
    [INFO] >>> maven-archetype-plugin:3.1.2:generate (default-cli) > generate-sources @ standalone-pom >>>
    [INFO]
    [INFO] <<< maven-archetype-plugin:3.1.2:generate (default-cli) < generate-sources @ standalone-pom <<<
    [INFO]
    [INFO]
    [INFO] --- maven-archetype-plugin:3.1.2:generate (default-cli) @ standalone-pom ---
    [INFO] Generating project in Interactive mode
    [INFO] Archetype [com.microsoft.azure:azure-functions-archetype:1.23] found in catalog remote
    Define value for property 'groupId' (should match expression '[A-Za-z0-9_\-\.]+'): com.fabrikam.functions
    [INFO] Using property: groupId = com.fabrikam.functions
    Define value for property 'artifactId' (should match expression '[A-Za-z0-9_\-\.]+'): fabrikam-functions
    [INFO] Using property: artifactId = fabrikam-functions
    Define value for property 'version' 1.0-SNAPSHOT: :
    Define value for property 'package' com.fabrikam.functions: : com.fabrikam.functions
    Define value for property 'appName' fabrikam-functions-20190909204519686: :
    Define value for property 'appRegion' westus: :
    Define value for property 'resourceGroup' java-functions-group: :
    Confirm properties configuration:
    groupId: com.fabrikam.functions
    groupId: com.fabrikam.functions
    artifactId: fabrikam-functions
    artifactId: fabrikam-functions
    version: 1.0-SNAPSHOT
    package: com.fabrikam.functions
    appName: fabrikam-functions-20190909204519686
    appRegion: westus
    resourceGroup: java-functions-group
    Y: : Y
    [INFO] ----------------------------------------------------------------------------
    [INFO] Using following parameters for creating project from Archetype: azure-functions-archetype:1.23
    [INFO] ----------------------------------------------------------------------------
    [INFO] Parameter: groupId, Value: com.fabrikam.functions
    [INFO] Parameter: artifactId, Value: fabrikam-functions
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.fabrikam.functions
    [INFO] Parameter: packageInPathFormat, Value: com/fabrikam/functions
    [INFO] Parameter: appName, Value: fabrikam-functions-20190909204519686
    [INFO] Parameter: resourceGroup, Value: java-functions-group
    [INFO] Parameter: package, Value: com.fabrikam.functions
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: groupId, Value: com.fabrikam.functions
    [INFO] Parameter: appRegion, Value: westus
    [INFO] Parameter: artifactId, Value: fabrikam-functions
    [INFO] Project created from Archetype in dir: C:\repos\blizzDevOpsDemos\AzureFunctionsJava\app2\fabrikam-functions
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time:  01:31 min
    [INFO] Finished at: 2019-09-09T20:46:08-04:00
    [INFO] ------------------------------------------------------------------------
    PS C:\repos\blizzDevOpsDemos\AzureFunctionsJava\app2> ls


        Directory: C:\repos\blizzDevOpsDemos\AzureFunctionsJava\app2

    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    d-----          9/9/2019  8:46 PM                fabrikam-functions

Note: Maven naming conventions: <https://maven.apache.org/guides/mini/guide-naming-conventions.html>

### Enable extension bundles. Put this in host.json

    {
        "version": "2.0",
        "extensionBundle": {
            "id": "Microsoft.Azure.Functions.ExtensionBundle",
            "version": "[1.*, 2.0.0)"
        }
    }

### Run the function locally

Change directory to the newly created project folder. Build and run the function from a CMD prompt

    cd fabrikam-function
    mvn clean package
    mvn azure-functions:run

### Test the app using curl

    PS C:\repos\blizzDevOpsDemos\AzureFunctionsJava\app2\fabrikam-functions> curl -w "\n" http://localhost:7071/api/HttpTrigger-Java -d Jim
    Hello, Jim

## Deploy to Azure

    az login
    mvn azure-functions:deploy

OUTPUT

    C:\repos\blizzDevOpsDemos\AzureFunctionsJava\app2\fabrikam-functions>mvn azure-functions:deploy
    [INFO] Scanning for projects...
    [INFO]
    [INFO] -------------< com.fabrikam.functions:fabrikam-functions >--------------
    [INFO] Building Azure Java Functions 1.0-SNAPSHOT
    [INFO] --------------------------------[ jar ]---------------------------------
    [INFO]
    [INFO] --- azure-functions-maven-plugin:1.3.3:deploy (default-cli) @ fabrikam-functions ---
    [INFO] Authenticate with Azure CLI 2.0
    [INFO] The specified function app does not exist. Creating a new function app...
    [INFO] Set function worker runtime to java
    [INFO] Successfully created the function app: fabrikam-functions-20190909204519686
    [INFO] Trying to deploy the function app...
    [INFO] Trying to deploy artifact to fabrikam-functions-20190909204519686...
    [INFO] Successfully deployed the artifact to https://fabrikam-functions-20190909204519686.azurewebsites.net
    [INFO] Successfully deployed the function app at https://fabrikam-functions-20190909204519686.azurewebsites.net
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time:  02:00 min
    [INFO] Finished at: 2019-09-09T20:55:49-04:00
    [INFO] ------------------------------------------------------------------------

### A test of the running function

Notice that the function url that was auto-generated has a runtime code because the source code was created with this inside: AuthorizationLevel.FUNCTION . . .

    https://fabrikam-functions-20190909204519686.azurewebsites.net/api/HttpTrigger-Java?code=Bx/Ao3hRUTe6IqLsMHe6RBDABM1rgxq6/lmVJWI3tmhZsuNjj6sCaQ==&name=Jim

And here is the url when I changed Auth level to AuthorizationLevel.ANONYMOUS . . .

    https://fabrikam-functions-20190909204519686.azurewebsites.net/api/HttpTrigger-Java?name=Mickey

### Azure resource info

Dashboard > Resource groups > java-functions-group > fabrikam-functions-20190909204519686

## Azure DevOps build / deploy

Note: I started off with a designer-based build definition that used the Azure App Service Deploy task. Despite spending hours troubleshooting it, I never was able to get it working properly. It said it was deploying the artifact, but the site would never update.

### From the build

(Using zipDeploy in the Azure App Service Deploy task)

#### Copy files

This is correct:

    Copying D:\a\1\s\app2\fabrikam-functions\target\azure-functions\fabrikam-functions-20190909204519686\fabrikam-functions-1.0-SNAPSHOT.jar to D:\a\1\a\app2\fabrikam-functions\target\azure-functions\fabrikam-functions-20190909204519686\fabrikam-functions-1.0-SNAPSHOT.jar

    Copying D:\a\1\s\app2\fabrikam-functions\target\azure-functions\fabrikam-functions-20190909204519686\host.json to D:\a\1\a\app2\fabrikam-functions\target\azure-functions\fabrikam-functions-20190909204519686\host.json

    Copying D:\a\1\s\app2\fabrikam-functions\target\azure-functions\fabrikam-functions-20190909204519686\HttpTrigger-Java\function.json to D:\a\1\a\app2\fabrikam-functions\target\azure-functions\fabrikam-functions-20190909204519686\HttpTrigger-Java\function.json

#### Archive files

    ##[debug]archiveFile=D:\a\1\a\196.zip

The zip file looks good with those 3 files.

### The app service deploy task ran

Making a programming change and pushing that to the repo. Let's see if it works.

### Here's the folder structure for d:\home\site\wwwroot after the publish (via: <https://fabrikam-functions-20190909204519686.scm.azurewebsites.net/DebugConsole>)

    09/09/2019  09:46 PM    <DIR>          HttpTrigger-Java
    09/09/2019  09:46 PM             4,540 fabrikam-functions-1.0-SNAPSHOT.jar
    09/09/2019  09:46 PM               145 host.json

### New deploy from local machine, then look at the folder structure again

    cd fabrikam-function
    mvn clean package
    mvn azure-functions:run

### Test the app again using curl

    PS C:\repos\blizzDevOpsDemos\AzureFunctionsJava\app2\fabrikam-functions> curl -w "\n" http://localhost:7071/api/HttpTrigger-Java -d Jim

### Deploy to Azure with the updates

    az login
    mvn azure-functions:deploy

### Folder structure of site after local deploy to Azure

    D:\home\site\wwwroot>dir
    Volume in drive D is Windows
    Volume Serial Number is 0211-4CB9

    Directory of D:\home\site\wwwroot

    09/10/2019  11:43 AM    <DIR>          HttpTrigger-Java
    09/10/2019  11:43 AM             4,560 fabrikam-functions-1.0-SNAPSHOT.jar
    09/10/2019  11:44 AM               145 host.json
                2 File(s)          4,705 bytes
                1 Dir(s)               0 bytes free

    D:\home\site\wwwroot>cd HttpTrigger-Java

    D:\home\site\wwwroot\HttpTrigger-Java>dir
    Volume in drive D is Windows
    Volume Serial Number is 0211-4CB9

    Directory of D:\home\site\wwwroot\HttpTrigger-Java

    09/10/2019  11:43 AM               371 function.json
                1 File(s)            371 bytes
                0 Dir(s)               0 bytes free

These are the files we want to see.

Here's what it looks like (with time stamps) from kudu

    /wwwroot  | 3 items
    Name	                                Modified	            Size

    HttpTrigger-Java	                    9/10/2019, 7:43:58 AM	
    
    fabrikam-functions-1.0-SNAPSHOT.jar	    9/10/2019, 7:43:58 AM   5 KB
    
    host.json	                            9/10/2019, 7:44:00 AM	1 KB

### Still using zipDeploy

After I pushed the code changes to the Az DevOps repo, the site still has the same structrue (good), but the files have the same timestamps (bad). The build isn't deploying properly.

### Changed the deploy task to use Web Deploy instead of a zip deploy

No difference.

### Changed to deploy from a package

Nope. Did not work.

Still troublelshooting.

Just saw this: <https://docs.microsoft.com/en-us/azure/azure-functions/run-functions-from-deployment-package> Need to add WEBSITE_RUN_FROM_PACKAGE in the function app settings.

Just checked - it's already there, and set to 1, which is what it should be in my case.

Here's what's in the SitePackages folder

    D:\home\data\SitePackages>dir
    Volume in drive D is Windows
    Volume Serial Number is 0211-4CB9

    Directory of D:\home\data\SitePackages

    09/10/2019  12:56 AM    <DIR>          .
    09/10/2019  12:56 AM    <DIR>          ..
    09/10/2019  12:56 AM             4,529 20190910005656.zip
    09/10/2019  01:36 AM             4,526 20190910013600.zip
    09/10/2019  01:50 AM             4,551 20190910015018.zip
    09/10/2019  03:52 PM             4,568 20190910155203.zip
    09/10/2019  03:52 PM                18 packagename.txt
                5 File(s)         18,192 bytes
                2 Dir(s)  5,497,558,007,808 bytes free

### For future reference: cURL deploy into KUDU

From your computer you can deploy using cURL instead of the mvn command. See <https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url>

## Completely overhauled the build definition

Based on this: <https://docs.microsoft.com/en-us/azure/devops/pipelines/ecosystems/java-function?view=azure-devops>

Note: This uses a yaml build instead of designer, and uses the *AzureFunctionApp* task.

### Build and deploy works now.

Afterwards I made some changes to allow the function to be in a subfolder instead of the root of the repo

The original sample had everything right off the root of the repo. Made it a bit difficult to have multiple independent functions.

### Here's the working yaml build definition

    # Maven
    # Build your Java project and run tests with Apache Maven.
    # Add steps that analyze code, save build artifacts, deploy, and more:
    # https://docs.microsoft.com/azure/devops/pipelines/languages/java

    trigger:
    - master

    pool:
    vmImage: 'ubuntu-latest'

    variables:
    # the name of the service connection that you created aboved serviceConnectionToAzure: connection-to-java-functions-group
    # the name of your web app here is the same one you used above
    # when you created the web app using the Azure CLI
    appName: fabrikam-functions-20190909204519686
    # The appFolderName variable allows the app to be anywhere in the folder structrure. 
    # Just have to let Maven know where it is.
    appFolderName: fabFunctionsApp
    steps:
    - task: Maven@3
    inputs:
        mavenPomFile: $(appFolderName)/pom.xml
        mavenOptions: '-Xmx3072m'
        javaHomeOption: 'JDKVersion'
        jdkVersionOption: '1.8'
        jdkArchitectureOption: 'x64'
        publishJUnitResults: true
        testResultsFiles: '**/surefire-reports/TEST-*.xml'
        goals: 'package'

    # to deploy to your app service
    - task: CopyFiles@2
    displayName: Copy Files
    inputs:
        SourceFolder: $(system.defaultworkingdirectory)/$(appFolderName)/target/azure-functions/
        Contents: '**'
        TargetFolder: $(build.artifactstagingdirectory)   

    - task: PublishBuildArtifacts@1
    displayName: Publish Artifact
    inputs:
        PathtoPublish: $(build.artifactstagingdirectory)    

    - task: AzureFunctionApp@1
    displayName: Azure Function App deploy
    inputs:
        azureSubscription: $(serviceConnectionToAzure)
        appType: functionApp
        appName: $(appName)
        package: $(build.artifactstagingdirectory)/$(appName)

### Here's the current folder structure on my computer

    Directory: C:\repos\DevOpsDemos\AzureFunctionsJava

    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    d-----         9/10/2019  6:19 PM                fabFunctionsApp
    d-----         9/10/2019  7:01 PM                templatefiles
    -a----         9/10/2019  5:28 PM            440 .gitignore
    -a----         9/11/2019  6:30 AM           1710 azure-pipelines.yml
    -a----         9/11/2019  6:44 AM          15294 readme.md

* *fabFunctionsApp* contains the Azure Function source code
* *templatefiles* contains the Azure ARM templates. They aren't used in this build. Will add the infrastructure deployment later.
* *azure-pipelines.yml* is the build / deploy pipeline definintion
* *readme.md* is this document
