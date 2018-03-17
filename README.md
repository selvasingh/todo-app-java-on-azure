# Deploy Todo List App =TO=> Tomcat in Azure App Service Linux

This Todo List app is a Java application. It provides end-to-end 
CRUD operation to todo list item from front-end AngularJS code. 
Behind the scene, todo list item data store 
is [Azure CosmosDB DocumentDB](https://docs.microsoft.com/en-us/azure/cosmos-db/documentdb-introduction). 
This application uses [Azure CosmosDB DocumentDB Spring Boot Starter](https://github.com/Microsoft/azure-spring-boot/tree/master/azure-starters/azure-documentdb-spring-boot-starter) 
and AngularJS to interact with Azure. This sample application 
provides several deployment options to deploy to Azure, pls 
see deployment section below. With Azure support in Spring 
Starters, maven plugins and Eclipse / IntelliJ plugins, 
Java application development and deployment on Azure
are effortless now.


## TOC

* [Requirements](#requirements)
* [Create Azure Cosmos DB documentDB](#create-azure-cosmos-db-documentdb)
* [Configuration](#configuration)
* [Run it](#run-it)
* [Contribution](#contribution)
* [Useful link](#useful-link)

## Requirements

* [JDK](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) 1.8 and above
* [Maven](https://maven.apache.org/) 3.0 and above
* [Tomcat](https://tomcat.apache.org/download-80.cgi) 8.5 and above

## Create Azure Cosmos DB documentDB

You can follow our steps using [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) to deploy an Azure Cosmos DB documentDB,
or follow [this article](https://docs.microsoft.com/en-us/azure/cosmos-db/create-documentdb-java) to create it from Azure portal.

1. login your Azure CLI, and set your subscription id 
    
    ```bash
    az login
    az account set -s <your-subscription-id>
    ```
1. create an Azure Resource Group, and note your group name

    ```bash
    az group create -n <your-azure-group-name> -l <your-resource-group-region>
    ```

1. create Azure Cosmos DB with DocumentDB kind. Note the `documentEndpoint` field in the response.

   ```bash
   az cosmosdb create --kind GlobalDocumentDB -g <your-azure-group-name> -n <your-azure-documentDB-name>
   ```
   **Note** name of cosmos db must be in lowercase.
   
1. get your Azure Cosmos DB key, get the `primaryMasterKey` of the DocumentDB you just created.

    ```bash
    az cosmosdb list-keys -g <your-azure-group-name> -n <your-azure-documentDB-name>
    ```

## Configuration

* Note your DocumentDB uri and key from last step, specify a database name but no need to create it.
  Pick an Azure Resource Group name and Web app name for App Service on Linux - you 
  can use an existing resource group and Web app or let the Maven plugin 
  create these for you. Set these values in system environment variables:

    ``` txt
    DOCUMENTDB_URI=put-your-documentdb-uri-here
    DOCUMENTDB_KEY=put-your-documentdb-key-here
    DOCUMENTDB_DBNAME=put-your-documentdb-databasename-here
    
    WEBAPP_RESOURCEGROUP_NAME=put-your-resourcegroup-name-here
    WEBAPP_NAME=put-your-webapp-name-here
    ```

## Build Todo List Web App (WAR)

Package the project using `mvn package`.

## Run it local = OPTIONAL STEP

1. Deploy the todo list app to local Tomcat `mvn cargo:deploy` 
(Tomcat must be running locally and set a system environment variable `TOMCAT_HOME=put-your-tomcat-home-here`)
1. Open `http://localhost:8080/todo-app-java-on-azure/#/TodoList` you can see the todo list app

## Deploy to Tomcat on Azure App Service on Linux

### Temporary Step - Until the Updated Maven Plugin for Azure Web Apps is released

Install a SNAPSHOT version of the Maven Plugin for Azure Web Apps:

```bash
git clone https://github.com/Microsoft/azure-maven-plugins.git
cd azure-maven-plugins
git checkout cs/wardeploy
mvn clean install -DskipTests
```
### Deploy to Tomcat on Azure App Service on Linux

Deploy in one step:

```bash
mvn azure-webapp:deploy
...
...
[INFO] Updating target Web App...
[INFO] Successfully updated Web App.
[INFO] Starting to deploy the war file...
[INFO] Successfully deployed Web App at https://todo-app-180317185136711.azurewebsites.net
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 04:01 min
[INFO] Finished at: 2018-03-17T13:00:06-07:00
[INFO] Final Memory: 51M/859M
[INFO] ------------------------------------------------------------------------

```

### Temporary Step - Until the App Service Team fixes this on the service-side

1. Go the Web App on Linux in the Azure Portal
2. Click on Development Tools / SSH
3. Click on Go --> to the app's SSH Shell

```bash
cd /home/site/wwwroot/webapps/ROOT
rm index.jsp
```

### Open the todo list Web app

Open it in a browser

![](./media/todo-list-app.jpg)

## Clean up

Delete the Azure resources you created by running the following command:

```bash
az group delete -y --no-wait -n <your-resource-group-name>
```

## Contributing

This project welcomes contributions and suggestions.  Most contributions require you to agree to a
Contributor License Agreement (CLA) declaring that you have the right to, and actually do, grant us
the rights to use your contribution. For details, visit https://cla.microsoft.com.

When you submit a pull request, a CLA-bot will automatically determine whether you need to provide
a CLA and decorate the PR appropriately (e.g., label, comment). Simply follow the instructions
provided by the bot. You will only need to do this once across all repos using our CLA.

This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/).
For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) or
contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any additional questions or comments.

## Useful link
- [Azure Spring Boot Starters](https://github.com/Microsoft/azure-spring-boot)
- [Azure Maven plugins](https://github.com/Microsoft/azure-maven-plugins)
