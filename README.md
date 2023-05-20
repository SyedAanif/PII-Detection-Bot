# PII-Detection-Bot
Steps to build a PII Detection Bot using Microsoft Bot Framework SDK and Azure Cognitive Service for Language.

## Introduction
**Personally Identifiable Information (PII)** detection is one of the features offered by [Azure Cognitive Service for Language](https://learn.microsoft.com/en-us/azure/cognitive-services/language-service/overview) which can identify, categorize, and even redact sensitive information in natural unstructured text. For example: phone numbers, email addresses, and forms of identification.
#### Redaction example:
![image](https://github.com/SyedAanif/PII-Detection-Bot/assets/66770875/76d19dd8-fb27-4ca5-8f91-b04d7082f5ad)

#### Categorization example:
![image](https://github.com/SyedAanif/PII-Detection-Bot/assets/66770875/0816ba17-6330-4139-b8ae-bbd3e940d0f5)

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
![image](https://github.com/SyedAanif/PII-Detection-Bot/assets/66770875/1bd764f6-81eb-4ff5-b959-077313dcc2e8)

3. Create an [Azure Cognitive Service for Language](https://learn.microsoft.com/en-us/azure/cognitive-services/language-service/overview) inside the Resource Group created above.
![image](https://github.com/SyedAanif/PII-Detection-Bot/assets/66770875/73e6e8ec-0beb-4e52-ae77-780b551e5ce6)
   3.1. After the Language Service is successfully created, navigate to the language resource to make a note of the `Keys` and `Endpoint` which will be used for accessing this resource.
![image](https://github.com/SyedAanif/PII-Detection-Bot/assets/66770875/047800fa-f979-4c0f-b69c-316c8fdf26d5)

4. Open a Python project(like the one explained in echo-bot) in editor of choice(like VSCode).

5. Install the required dependencies as shown in the `requirements.txt` file using the command:
  ```
  pip install -r requirements.txt
  ```
  This will install the dependencies like [`botbuilder-core`](https://pypi.org/project/botbuilder-core/), [`asyncio`](https://pypi.org/project/asyncio/), [`aiohttp`](https://pypi.org/project/aiohttp/), [`cookiecutter`](https://pypi.org/project/cookiecutter/)
  ![image](https://github.com/SyedAanif/PII-Detection-Bot/assets/66770875/c6fcd17f-cabc-4e86-b68f-403dd1cb73aa)
  
6. Login to Microsoft Azure Account and set your subscription:
    ```
    az login

    az account set --subscription "<< subscription-id >>"
    ```
    ![image](https://github.com/SyedAanif/PII-Detection-Bot/assets/66770875/39f7adf2-be48-4494-a47f-eb4c59d49480)

7. Create an [App Registration](https://learn.microsoft.com/en-us/power-apps/developer/data-platform/walkthrough-register-app-azure-active-directory) in the Azure portal and make a note of its `Application (client) ID` and `appSecret`. 
  ![image](https://github.com/SyedAanif/PII-Detection-Bot/assets/66770875/666c68ad-4d83-40af-a0d7-70e7d0deb430)
  ![image](https://github.com/SyedAanif/PII-Detection-Bot/assets/66770875/282d9e4e-8fe6-46a4-98fe-5ff8d792086e)

8. Update the `config.py` file in VSCode with the copied `appId` and `appSecret` in the `MicrosoftAppId` and `MicrosoftAppPassword` respectively.
  ![image](https://github.com/SyedAanif/PII-Detection-Bot/assets/66770875/960d85c1-5662-4a91-a9b7-8278c4b64c7c)

9. Update `bot.py` with the `Keys` and `Endpoint` of the `Azure Language Service` which are used to authenticate and connect to the `text-analytics-client`. The `text-analytics-client` is used to recognize PII entities in the input user text. The bot contains mainly two functions:
  + `on_members_added_activity`: this function gets called whenever a user accesses the bot and greets the user with "Hello and welcome to the PII detection bot".
  + `on_message_activity`: this function gets called whenever a user interacts with the bot. This function takes the user input text and calls the text-analytics-client for PII recognition. This function prints the redacted text and the PII categories of the redacted text.
  ![image](https://github.com/SyedAanif/PII-Detection-Bot/assets/66770875/b6579bee-c3da-4db3-a5a0-fbbdb08cda98)

10. Create an [App Service](https://learn.microsoft.com/en-us/azure/app-service/overview) and [App Service Plan](https://learn.microsoft.com/en-us/azure/app-service/overview-hosting-plans?source=recommendations) with [Azure Resource Manager(ARM)](https://learn.microsoft.com/en-us/azure/azure-resource-manager/templates/overview) template:
    10.1. Modify the `parameters-for-template-BotApp-with-rg.json` with values for `appServiceName`, `newAppServicePlanName`, `newAppServicePlanLocation`, `appId`, `appSecret`
    ![image](https://github.com/SyedAanif/PII-Detection-Bot/assets/66770875/e15dc353-8224-4c96-b88a-53e847dbe0bf)
    10.2. Change directory to `deploymentTemplates\deployUseExistResourceGroup\`
      ```
      cd .\deploymentTemplates\deployUseExistResourceGroup\
      ```
    10.3. Create a deployment for `App Service` and `App Service Plan` with ARM template-file `template-BotApp-with-rg.json`:
      ```
       az deployment group create --resource-group "<<Resource-Group-Name>>" --template-file "<<template-file-json>>" --parameters "@<<parameters-file-json>>"
      ```
      ![image](https://github.com/SyedAanif/PII-Detection-Bot/assets/66770875/0e27b63c-0c22-4bea-bfaa-7ea09ee49448)

11. Create an [Azure Bot Service](https://azure.microsoft.com/en-in/products/bot-services/?ef_id=_k_CjwKCAjw36GjBhAkEiwAKwIWyU8eyHxp5IuTW7RCGqs8J43g1lEkqUtbyjuGaas-tBdrIB6pkX8_DxoCpYUQAvD_BwE_k_&OCID=AIDcmmf1elj9v5_SEM__k_CjwKCAjw36GjBhAkEiwAKwIWyU8eyHxp5IuTW7RCGqs8J43g1lEkqUtbyjuGaas-tBdrIB6pkX8_DxoCpYUQAvD_BwE_k_&gclid=CjwKCAjw36GjBhAkEiwAKwIWyU8eyHxp5IuTW7RCGqs8J43g1lEkqUtbyjuGaas-tBdrIB6pkX8_DxoCpYUQAvD_BwE) with [Azure Resource Manager(ARM)](https://learn.microsoft.com/en-us/azure/azure-resource-manager/templates/overview) template:
    11.1. Modify the `parameters-for-template-AzureBot-with-rg.json` with values for `azureBotId`, `botEndpoint`, `appId`.
      ![image](https://github.com/SyedAanif/PII-Detection-Bot/assets/66770875/9353d494-cecd-4e91-8df3-058013b62927)
  
     11.2. Create a deployment for `Azure Bot Service` with ARM template-file `template-AzureBot-with-rg.json`:
      ```
       az deployment group create --resource-group "<<Resource-Group-Name>>" --template-file "<<template-file-json>>" --parameters "@<<parameters-file-json>>"
      ```
      ![image](https://github.com/SyedAanif/PII-Detection-Bot/assets/66770875/5a0e7014-7736-4056-b709-f4d320c96717)

12. The creation of `Azure Bot Service`, `App Service`, `App Service Plan` can be verified by navigating to the Azure portal and the respective Resource Group.
    ![image](https://github.com/SyedAanif/PII-Detection-Bot/assets/66770875/c4d34d17-ad1f-4175-8e38-9ecdcb0ff12f)

13. Package the Microsoft Bot Framework SDK(python code) into a `.zip` file.

14. Deploy the packaged SDK as a `web-app`"
    14.1. Change the working directory to the root of the project.

    14.2. Run the command:
        ```
        az webapp deployment source config-zip --resource-group "<<resource-group-name>>" --name "<<app-servicename>>" --src       "<<packaged-zip-code>>"
        ```

    14.2. If the command runs successfully, it will deploy the Bot framework SDK as a web-application and the deployment endpoint responds with an HTTP status code 202.
        ![image](https://github.com/SyedAanif/PII-Detection-Bot/assets/66770875/aecc71c3-4e5c-4822-81f8-67f57d8ba59e)

15. The deployed `Azure Bot PII service` can be tested in the Azure portal with any example as shown below:
    15.1: Example 1:
          ```
          My name is John Doe and my phone number is 1234567890. I can be contacted over john.doe@gmail.com. I live in Seattle.
          ```
          ![image](https://github.com/SyedAanif/PII-Detection-Bot/assets/66770875/41984048-275e-49f5-9b91-6b635a948020)

     15.2. Example 2:
           ```
           Barack Obama is an American politician who served as the 44th President of the United States from 2009 to 2017. He is the first African American to have served as president, as well as the first born outside the contiguous United States.
           ```
           ![image](https://github.com/SyedAanif/PII-Detection-Bot/assets/66770875/4809f516-e3a6-4f4e-9203-421e9f17242b)

     15.3. Example 3:
           ```
           You can even pre-order from their online menu at www.contososteakhouse.com, call 312-555-0176 or send email to order@contososteakhouse.com!
           ```
           ![image](https://github.com/SyedAanif/PII-Detection-Bot/assets/66770875/d5646623-5fcc-4438-b3a4-c2f84acc0b23)

         

