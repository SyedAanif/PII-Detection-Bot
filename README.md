# PII-Detection-Bot
Steps to build a PII Detection Bot using Microsoft Bot Framework SDK and Azure Cognitive Service for Language.

## Introduction
**Personally Identifiable Information (PII)** detection is one of the features offered by [Azure Cognitive Service for Language](https://learn.microsoft.com/en-us/azure/cognitive-services/language-service/overview) which can identify, categorize, and even redact sensitive information in natural unstructured text. For example: phone numbers, email addresses, and forms of identification.

#### Redaction example:
  ![Screenshot 2023-05-20 102209](https://github.com/SyedAanif/PII-Detection-Bot/assets/66770875/696c5721-b028-47b3-ad69-e6c05b4a2030)

#### Categorization example:
  ![Screenshot 2023-05-20 101646](https://github.com/SyedAanif/PII-Detection-Bot/assets/66770875/be997c9f-939d-4065-abf1-25a901a28d46)

Here, we will be building this bot using:
- [Azure Cognitive Service for Language](https://learn.microsoft.com/en-us/azure/cognitive-services/language-service/overview) which is a cloud-based service for Natural Language Processing(NLP) helping in understanding and analyzing text.
- [Azure Bot Service](https://learn.microsoft.com/en-us/azure/bot-service/bot-builder-basics?view=azure-bot-service-4.0) which is a cloud platform to host bots and make them available to channels like Facebook, MS Teams etc.
- [Microsoft Bot Framework SDK](https://github.com/microsoft/botframework-sdk) which is used to develop conversational applications in Python, Java, JavaScript and C#, that can be hosted on Azure Bot Service.

## Pre-requisites
- Python 3.X version: 
  + [Installation using Anaconda distribution](https://docs.anaconda.com/free/anaconda/#installation)
  + [Python Website](https://www.python.org/downloads/)
- [Microsoft Azure Account](https://azure.microsoft.com/en-in/free/)
- Editor of choice:
  + [VSCode](https://code.visualstudio.com/download)
- Terminal of choice with [Azure CLI](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli) installed.

## Steps to create the PII-Detection-Bot
1. Sign-in to your [Microsoft Azure account](https://portal.azure.com/#home).

2. Create a [Resource Group](https://learn.microsoft.com/en-us/azure/azure-resource-manager/management/manage-resource-groups-portal#what-is-a-resource-group) (in a region closest to your location) to logically contain your Azure Resources.

   ![Screenshot 2023-05-20 113534](https://github.com/SyedAanif/PII-Detection-Bot/assets/66770875/f7a82b10-b641-4612-aea9-985723b7128e)

3. Create an [Azure Cognitive Service for Language](https://learn.microsoft.com/en-us/azure/cognitive-services/language-service/overview) inside the Resource Group created above.

   ![Screenshot 2023-05-20 113912](https://github.com/SyedAanif/PII-Detection-Bot/assets/66770875/be3ac12c-f660-4865-9b0f-c9f8311c7371)

    + After the Language Service is successfully created, navigate to the language resource to make a note of the `Keys` and `Endpoint` which will be used for accessing this resource.
   
      ![Screenshot 2023-05-20 114258](https://github.com/SyedAanif/PII-Detection-Bot/assets/66770875/6aa900f7-c768-488e-99b0-3b4ac98d70e9)

4. Open a Python project([like the one explained in echo-bot](https://github.com/microsoft/BotBuilder-Samples/tree/main/samples/python/02.echo-bot)) in editor of choice(like VSCode).

5. Install the required dependencies as shown in the `requirements.txt` file using the command:
    ```
    pip install -r requirements.txt
    ```
    This will install the dependencies like [`botbuilder-core`](https://pypi.org/project/botbuilder-core/), [`asyncio`](https://pypi.org/project/asyncio/), [`aiohttp`](https://pypi.org/project/aiohttp/), [`cookiecutter`](https://pypi.org/project/cookiecutter/)

   ![Screenshot 2023-05-20 121842](https://github.com/SyedAanif/PII-Detection-Bot/assets/66770875/525b646a-857c-4d21-bca4-fcf4c5cb03c6)

  
6. Login to Microsoft Azure Account and set your subscription:
    ```
    az login

    az account set --subscription "<< subscription-id >>"
    ```

   ![Screenshot 2023-05-20 124003](https://github.com/SyedAanif/PII-Detection-Bot/assets/66770875/da992726-13eb-4dcc-aae1-44b1efbe90b0)

7. Create an [App Registration](https://learn.microsoft.com/en-us/power-apps/developer/data-platform/walkthrough-register-app-azure-active-directory) in the Azure portal and make a note of its `Application (client) ID` and `appSecret`.

     ![Screenshot 2023-05-20 124805](https://github.com/SyedAanif/PII-Detection-Bot/assets/66770875/1cb729cb-1492-4a32-80a9-892e0f34569b)

     ![Screenshot 2023-05-20 124912](https://github.com/SyedAanif/PII-Detection-Bot/assets/66770875/ece3c164-f7a1-47d3-8f7c-27741774f7d6)

8. Update the `config.py` file in VSCode with the copied `appId` and `appSecret` in the `MicrosoftAppId` and `MicrosoftAppPassword` respectively.
   
    ![Screenshot 2023-05-20 125156](https://github.com/SyedAanif/PII-Detection-Bot/assets/66770875/b3115477-8ebd-48e2-8efe-d516be1e1093)

9. Update `bot.py` with the `Keys` and `Endpoint` of the `Azure Language Service` which are used to authenticate and connect to the `text-analytics-client`. The `text-analytics-client` is used to recognize PII entities in the input user text. The bot contains mainly two functions:
    + `on_members_added_activity`: this function gets called whenever a user accesses the bot and greets the user with "Hello and welcome to the PII detection bot".
    + `on_message_activity`: this function gets called whenever a user interacts with the bot. This function takes the user input text and calls the text-analytics-client for PII recognition. This function prints the redacted text and the PII categories of the redacted text.

      ![Screenshot 2023-05-20 130456](https://github.com/SyedAanif/PII-Detection-Bot/assets/66770875/b29952ac-3171-430f-b548-e56307e77df1)

10. Create an [App Service](https://learn.microsoft.com/en-us/azure/app-service/overview) and [App Service Plan](https://learn.microsoft.com/en-us/azure/app-service/overview-hosting-plans?source=recommendations) with [Azure Resource Manager(ARM)](https://learn.microsoft.com/en-us/azure/azure-resource-manager/templates/overview) template:
    + Modify the `parameters-for-template-BotApp-with-rg.json` with values for `appServiceName`, `newAppServicePlanName`, `newAppServicePlanLocation`, `appId`, `appSecret`
    
      ![Screenshot 2023-05-20 131933](https://github.com/SyedAanif/PII-Detection-Bot/assets/66770875/06470c2d-640a-44c0-b16e-d4dba4ca462f)

    + Change directory to `deploymentTemplates\deployUseExistResourceGroup\`
      ```
      cd .\deploymentTemplates\deployUseExistResourceGroup\
      
      ```
    + Create a deployment for `App Service` and `App Service Plan` with ARM template-file `template-BotApp-with-rg.json`:
      ```
       az deployment group create --resource-group "<<Resource-Group-Name>>" --template-file "<<template-file-json>>" --parameters "@<<parameters-file-json>>"
      
      ```

        ![Screenshot 2023-05-20 132929](https://github.com/SyedAanif/PII-Detection-Bot/assets/66770875/b6eeeb70-8b56-4e7d-89f5-c0e0052eac45)

11. Create an [Azure Bot Service](https://azure.microsoft.com/en-in/products/bot-services/?ef_id=_k_CjwKCAjw36GjBhAkEiwAKwIWyU8eyHxp5IuTW7RCGqs8J43g1lEkqUtbyjuGaas-tBdrIB6pkX8_DxoCpYUQAvD_BwE_k_&OCID=AIDcmmf1elj9v5_SEM__k_CjwKCAjw36GjBhAkEiwAKwIWyU8eyHxp5IuTW7RCGqs8J43g1lEkqUtbyjuGaas-tBdrIB6pkX8_DxoCpYUQAvD_BwE_k_&gclid=CjwKCAjw36GjBhAkEiwAKwIWyU8eyHxp5IuTW7RCGqs8J43g1lEkqUtbyjuGaas-tBdrIB6pkX8_DxoCpYUQAvD_BwE) with [Azure Resource Manager(ARM)](https://learn.microsoft.com/en-us/azure/azure-resource-manager/templates/overview) template:
    + Modify the `parameters-for-template-AzureBot-with-rg.json` with values for `azureBotId`, `botEndpoint`, `appId`.

      ![Screenshot 2023-05-20 133651](https://github.com/SyedAanif/PII-Detection-Bot/assets/66770875/6b61ef51-0bf4-4d9c-bc38-66b3d8539a7b)

    + Create a deployment for `Azure Bot Service` with ARM template-file `template-AzureBot-with-rg.json`:
      ```
       az deployment group create --resource-group "<<Resource-Group-Name>>" --template-file "<<template-file-json>>" --parameters "@<<parameters-file-json>>"
      
      ```

        ![Screenshot 2023-05-20 134242](https://github.com/SyedAanif/PII-Detection-Bot/assets/66770875/8d09bc2a-1908-40db-8dd6-eed430d3c89d)

12. The creation of `Azure Bot Service`, `App Service`, `App Service Plan` can be verified by navigating to the Azure portal and the respective Resource Group.

    ![Screenshot 2023-05-20 142641](https://github.com/SyedAanif/PII-Detection-Bot/assets/66770875/5d3bbcc1-7abf-4ca4-9807-bf99d2e698c5)

13. Package the Microsoft Bot Framework SDK(python code) into a `.zip` file.

14. Deploy the packaged SDK as a `web-app`
    + Change the working directory to the root of the project.

    + Run the command:
      ```
      az webapp deployment source config-zip --resource-group "<<resource-group-name>>" --name "<<app-servicename>>" --src "<<packaged-zip-code>>"
      
      ```

    + If the command runs successfully, it will deploy the Bot framework SDK as a web-application and the deployment endpoint responds with an HTTP status code 202.

        ![Screenshot 2023-05-20 144822](https://github.com/SyedAanif/PII-Detection-Bot/assets/66770875/8460b302-7dcd-4459-9c7f-08a7cfae7cd5)

15. The deployed `Azure Bot PII service` can be tested in the Azure portal with any example as shown below:
    + Example 1:
          ```
          My name is John Doe and my phone number is 1234567890. I can be contacted over john.doe@gmail.com. I live in Seattle.
          ```

        ![Screenshot 2023-05-20 145344](https://github.com/SyedAanif/PII-Detection-Bot/assets/66770875/814584f7-2955-4fbe-9dff-df3c81e7ee48)
      
     + Example 2:
           ```
           Barack Obama is an American politician who served as the 44th President of the United States from 2009 to 2017. He is the first African American to have served as president, as well as the first born outside the contiguous United States.
           ```
       
         ![Screenshot 2023-05-20 145659](https://github.com/SyedAanif/PII-Detection-Bot/assets/66770875/ea8f291a-2d4e-4620-838f-2746c6e2a3d9)

     + Example 3:
           ```
           You can even pre-order from their online menu at www.contososteakhouse.com, call 312-555-0176 or send email to order@contososteakhouse.com!
           ```
       
         ![Screenshot 2023-05-20 150058](https://github.com/SyedAanif/PII-Detection-Bot/assets/66770875/68ed5ff3-2bd2-472f-a668-f916f98644b7)
