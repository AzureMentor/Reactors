# Creating Azure resources

[Prerequisite: Exploring app.py](./explore-app-py.md)

With our Flask application created, it's time to turn our attention to incorporating Computer Vision. Computer Vision is a part of [Cognitive Services](https://azure.microsoft.com/services/cognitive-services), a suite of AI tools built by Microsoft.

We will explore how to create keys our keys.

## Overview of Computer Vision

Azure Cognitive Services is a set of more than 20 services and APIs for infusing intelligence backed by machine learning and neural networks into the applications that you write. One member of the Cognitive Services family is the [Computer Vision API](https://azure.microsoft.com/services/cognitive-services/computer-vision/), which can analyze images uploaded to it and:

- Identify objects in the images.
- Generate captions for the images (for example, "A woman riding a bicycle").
- Use Optical Character Recognition (OCR) to extract text from the images.
- Find faces in the images and identify attributes of those faces, such as age and gender.
- Generate "smart thumbnails" centered around the subjects of the images.
- Recognize famous people and landmarks in the images.

## Azure Command-Line Interface (CLI)

If you aren't familiar with the Azure CLI, you can learn more about it and the numerous commands it supports in [Get started with Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest). Most operations in Azure can be performed by using either the CLI or the [Azure Portal](https://portal.azure.com). Power users tend to prefer the CLI, in part because CLI commands can be used in scripts to automate repetitive tasks.

### Install the Azure (CLI)

The [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest) is a command-line environment for creating and managing Azure resources. Versions are available for Windows, macOS, and Linux. In subsequent unites you'll use the Azure CLI to create various Azure resources. Here you will install the Azure CLI and login to Azure.

If you haven't already installed Azure CLI, you can visit the [Azure CLI web page](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) and follow the installation instructions to perform the installation. You can determine if it's already installed by running `az -v` in a console window, which will display the verson number.

Alternatively, you can visit [Azure Cloud Shell](https://shell.azure.com) to access the Azure CLI via a website.

### Login to Azure using the Azure CLI

If you are using a locally installed instance of the Azure CLI, you will need to log into Azure when first using the tool. The process will be completed using a browser window (the command will automatically open it); the browser can be closed once login is complete. If you are using Azure Cloud Shell, no login is required.

``` bash
az login
```

### Choose subscription (optional)

You may have multiple subscriptions for Azure associated with your account. We will be using the default subscription. If you aren't sure which one the Azure CLI is using, or you need to change it, you can use the following commands.

``` bash
# List all subscriptions
az account list

# Change the default (if needed)
az account set -s <SUBSCRIPTION_ID>
```

## Azure resources

Our scenario involves creating a web application which will use Cognitive Services. We will need a [Resource Group](#resource-group), [Cognitive Services key](#cognitive-services-key) for AI, and [App Services](#app-services) for web hosting.

### Resource Group

Resource groups are a key component of [resource management](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) in Azure. They act as containers for other Azure resources and serve to group those resources together so that you can view billing information for them as a group, apply security rules as a group, and even delete them as a group. Every Azure resource that you create must be part of a resource group.

### Cognitive Services Key

In order to use any Cognitive Service, you must first obtain an API key. This key travels in each request you place in an HTTP header named `Ocp-Apim-Subscription-Key`. It is Azure's way of authenticating the caller and determining which Azure subscription to bill calls to. Most Azure Cognitive Service APIs have free tiers for which no billing is performed, but if you plan to place thousands of calls a day to a Cognitive Services API, you will be billed for it through your Azure subscription.

You can create a separate key for each service, or create an [All-in-One](https://portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) key. The All-in-One key supports every Cognitive Service, with the exception of QnA Maker, Speech Services and Custom Vision (not to be mistaken with Computer Vision, which is what we are using). The All-in-One key is only available as a paid service at this time. If you wish to use the free tier, you will need to create a key for each service.

> **Note:** For purposes of this demo, we will be creating a single key as this is the recommended option for using Azure Cognitive Services. You can consult the [documentation for information on pricing](https://azure.microsoft.com/pricing/details/cognitive-services/). At the time of this writing, 1,000 transactions for Face API (as an example) costs $1 US; we will be executing less than 50 transactions. Your instructor will also provide a key if you so desire.

### App Services

> **NOTE:** When choosing locations, it's important to ensure all related services are created in the same location. Making calls across various Azure locations is the leading cause of poor performing applications on Azure. For our purposes, we're going to locate everything in **northcentralus**.

## Resource creation

To ease the creation and configuration of necessary resources, we will use an [Azure CLI extension](https://docs.microsoft.com/cli/azure/azure-cli-extensions-overview?view=azure-cli-latest) named [hack](https://github.com/microsoft/hackwithazure/blob/master/az-hack.md). The **hack** extension is designed to quickly create resources commonly used for hackathon projects or other samples. By using **hack** we can create a resource group, an instance of App Services, a database, and a key for Cognitive Services.

In addition, the tool will add all keys and values as [App Settings in the newly created web app](https://docs.microsoft.com/azure/app-service/configure-common), which can be accessed as environmental variables by your application. This allows you to easily read the values without hard coding them into your application. We'll be using [dotenv](https://github.com/theskumar/python-dotenv) to manage these values when running our application locally.

### Installing the extension

In the Cloud Shell or terminal or command window you're using to access the Azure CLI, install `az hack` by executing the following:

``` terminal
az extension add -n hack
```

### Run the extension

We will execute `az hack create` by specifying a runtime of **Python**, a database of type **MySQL** (although we won't be using a database as part of our project), a location of **northcentralus**, and to enable AI through Cognitive Services. 

> **NOTE:** When the command completes, **copy the output and paste it into Notepad or another location**; we'll need it a little later.

``` terminal
az hack create --name reactor --runtime python --location northcentralus --ai --output yaml
```

> **NOTE:** When choosing locations, it's important to ensure all related services are created in the same location. Making calls across various Azure locations is the leading cause of poor performing applications on Azure. For our purposes, we're going to locate everything in **northcentralus**.

#### What's going on?

`az hack create` takes a series of parameters:

- `name`, which will be the root name used for all items created
- `runtime`, to indicate what runtime we'll be using for our application, such as Node.js (node) or PHP
- `ai`, to create a Cognitive Services All in One key
- `location`, to indicate the Azure region into which we want to create our resources
- `output`, which we set to **yaml**, which allows us to control the output from the command (makes it easier to copy/paste later)

The utility will add five random characters to the end of the name you provide to ensure uniqueness. If you later decide you want to take your application to production, you can always upgrade the plan the App Service is running on and then [provide a custom domain](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain).

## Summary and next steps

We've explored how to create keys for Computer Vision Service and Translator Text. Next, we'll see how we [can call Computer Vision](./computer-vision.md) from our application.
