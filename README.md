## Objective

> **⚠️ IMPORTANT NOTICE - Azure OpenAI Model Availability in UAE North**
>
> As of early 2026, **Azure OpenAI Service models (including GPT-4o, GPT-4, and GPT-3.5) are NOT generally available in the UAE North region**. While Azure AI Foundry and Azure Machine Learning infrastructure are supported in UAE North, specific AI model deployments may have limited or no availability. 
>
> **Key Limitations:**
> - GPT-4o and GPT-4 models are in gradual rollout and not guaranteed for all customers or deployment types in UAE North
> - Model inference requests from UAE North may be routed to other regions (e.g., West Europe, France Central), which may not satisfy strict data residency requirements
> - Quota requests for these models in UAE North may be restricted or unavailable
>
> **Before deploying to UAE North:**
> 1. Verify current model availability in the [official Azure OpenAI model availability matrix](https://learn.microsoft.com/en-us/azure/ai-services/openai/concepts/models)
> 2. Consider alternative regions with broader model support (e.g., Sweden Central, East US 2) if specific models are critical to your deployment
> 3. Contact Microsoft support for the latest information on UAE North model availability for your subscription
>
> **This repository serves as a template** that must be adapted based on your specific requirements and actual service availability in your target region.

This Sovereign AI Landing Zone (SAIL) repository provides a secure foundation for deploying AI models within UAE's borders on Azure, so organizations can build, scale, and innovate while maintaining the highest standards of privacy and compliance. As the initial focus, we consider sovereignty on Azure as satisfying two key requirements:

* Data **at rest** should be stored within UAE Azure data centres
* Data **in-transit** should be processed within UAE Azure data centres

The critical Azure services in supporting the deployment of sovereign AI models in UAE are [Microsoft Foundry](https://learn.microsoft.com/en-us/azure/ai-foundry/what-is-azure-ai-foundry?view=foundry&preserve-view=true) and [Azure Machine Learning](https://learn.microsoft.com/en-us/azure/machine-learning/overview-what-is-azure-machine-learning?view=azureml-api-2).

We will provide a comprehensive review of deployment approaches and templates for AI models satisfying the two soverignity requirements of data at rest and in-transit staying within UAE borders. Initial Azure Bicep scripts for deployment of Azure Machine Learning and Microsoft Foundry through Infrastructure as Code (IaC) can be found in the ```infra``` folder. More updates to the IaC scripts and deployment scripts to come!

## Microsoft Foundry AI model deployment options

For soverignity reasons, it would be important to consider AI models deployable within Microsoft Foundry from the list of [Directly Sold by Azure](https://learn.microsoft.com/en-us/azure/ai-foundry/foundry-models/concepts/models-sold-directly-by-azure?view=foundry&tabs=global-standard-aoai%2Cstandard-chat-completions%2Cglobal-standard&pivots=azure-direct-others) models which satisfy deployment requirements from a data security and privacy perspective as outlined [here](https://learn.microsoft.com/en-us/azure/ai-foundry/responsible-ai/openai/data-privacy?view=foundry&tabs=azure-portal). 

In particular for models from the Directly Sold by Azure list within Microsoft Foundry:

* Data at rest is stored in the Foundry resource in the customer's Azure tenant, within the same geography as the resource. For UAE, the geography is [UAE North _and_ UAE Central](https://learn.microsoft.com/en-us/azure/reliability/regions-list#azure-regions-list-1). Generally prompts and completions for such models are not stored [except as part of specific features](https://learn.microsoft.com/en-us/azure/ai-foundry/responsible-ai/openai/data-privacy?view=foundry&tabs=azure-portal#data-storage-for-azure-direct-models-features) such as fine-tuning and Assistant API. Another default-enabled temporary data storage feature is to [defend against abuse](https://learn.microsoft.com/en-us/azure/ai-foundry/responsible-ai/openai/data-privacy?view=foundry&tabs=azure-portal#preventing-abuse) where potentially abusive material from prompts and completions may be stored up to 30 days for the sole purpose of Microsoft review. This feature can be disabled by submitting this [form](https://customervoice.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR7en2Ais5pxKtso_Pz4b1_xUOE9MUTFMUlpBNk5IQlZWWkcyUEpWWEhGOCQlQCN0PWcu). 

* Data in-transit can be processed in various forms depending on the [model deployment type](https://learn.microsoft.com/en-us/azure/ai-foundry/foundry-models/concepts/deployment-types?view=foundry). To ensure that AI models through AI Foundry process data in-transit within UAE Azure regions, they must be deployed as either
  * Standard for Pay-As-You-Go deployments
  * Regional Provisioned for Provisioned Throughput Unit - PTU (dedicated capacity with guaranteed units of throughput) deployments

* Alternatively, global deployment type means that data might be processed for inferencing in any Foundry location in the world. Data zone is not applicable for UAE as only US and Europe regions have [Data Zone support](https://azure.microsoft.com/en-us/blog/announcing-the-availability-of-azure-openai-data-zones-and-latest-updates-from-azure-ai/?msockid=140ffb7f5488655f0412ed745540640a). 

* As of early 2026, **Azure OpenAI models have limited availability in UAE North**. The following models were historically available in other regions but should be verified for UAE North before deployment:
  * Standard for [Pay-As-You-Go deployments](https://learn.microsoft.com/en-us/azure/ai-foundry/foundry-models/concepts/models-sold-directly-by-azure?view=foundry&preserve-view=true&tabs=global-standard-aoai%2Cstandard-chat-completions%2Cglobal-standard&pivots=azure-openai#standard-deployment-regional-models-by-endpoint) (availability in UAE North region should be verified):
    * gpt-3.5 turbo models (Versions 1106, 0125)
    * gpt-4o (Version 1120)
    * text embedding models (ada, 3-large, 3-small)
  * Regional [Provisioned Throughput Units (PTU) deployments](https://learn.microsoft.com/en-us/azure/ai-foundry/foundry-models/concepts/models-sold-directly-by-azure?view=foundry&preserve-view=true&tabs=provisioned%2Cstandard-chat-completions%2Cglobal-standard&pivots=azure-openai#provisioned-deployment-model-availability) (availability in UAE North region should be verified):
    * o3-mini
    * gpt-5
    * gpt-4o (Versions 1120, 0806, 0513)
    * gpt-4o-mini

  **⚠️ Warning**: Always check the [latest Azure OpenAI model availability documentation](https://learn.microsoft.com/en-us/azure/ai-services/openai/concepts/models) before deployment, as availability varies by region, subscription type, and is subject to change.

* There are also certain AI models that could be deployed using the Microsoft Foundry (classic) hub-based service using managed compute, such as [certain Cohere models](https://learn.microsoft.com/en-us/azure/ai-foundry/foundry-models/concepts/models-sold-directly-by-azure?view=foundry&preserve-view=true&tabs=global-standard-aoai%2Cstandard-chat-completions%2Cglobal-standard&pivots=azure-direct-others#cohere-models-sold-directly-by-azure) from the Directly Sold by Azure list. Such models would be deployed on managed GPU VMs to ensure data in-transit and data at rest remains in UAE geography in a Hub-based Foundry resource, which is based on the Azure ML deployment infrastructure as seen below.

## Azure Machine Learning AI model deployment options

The following is guidance to facilitate deployment of generic AI models including large language models (LLMs) on Azure Machine Learning's (AML) Managed Online Endpoints for efficient, scalable, and secure real-time inference.​ Two patterns of deployment types are described: models through vLLM and generic AI models. By leveraging AML's [Managed Online Endpoints](https://learn.microsoft.com/en-us/azure/machine-learning/how-to-deploy-online-endpoints?view=azureml-api-2&tabs=cli), the model would be deployed within the AML region and secured through inbound and outbound private connections thus ensuring a secured and sovereign solution.

In particular, this pattern gives you the ability to utilize OOTB Hugging Face models onto Managed Online Endpoints in AML.

 ### Pre-requisites : 

1. vLLM: A high-throughput, memory-efficient inference engine designed for LLMs.​ We will be creating a custom Dockerized environment for vLLM on AML as a foundational step.
2. (Optional) You can also bring in any generic AI models by leveraging the custom Dockerfile and providing a generic score.py file that loads the model in memory and defines inferencing.
3. Managed Online Endpoints: A feature in Azure Machine Learning that simplifies deploying machine learning models for real-time inference by handling serving, scaling, securing, and monitoring complexities.​ At the time of writing, an additional context to using this feature is to ensure data and regional residency abilities that could be achieved through the setup here.
4. Model of your choice from HuggingFace (or any generic AI model). Knowledge around usage of HuggingFace models and the workflow and AuthN aspects are assumed.

### Key Deployment Steps:

1. Create a Custom Environment on AzureML: Define a Dockerfile specifying the environment for the model, utilizing vLLM's base container with necessary dependencies.​

2. Deploy the AzureML Managed Online Endpoint: Configure the endpoint and deployment settings using YAML files, specifying the model to deploy, environment variables, and instance configurations.​

3. Test the Deployment: Retrieve the endpoint's scoring URI and API keys, then send test requests to ensure the model is serving correctly.​ Using MS Entra for authentication and authorization is supported as well: https://learn.microsoft.com/en-us/azure/machine-learning/concept-endpoints-online-auth?view=azureml-api-2

4  (Optional) - Autoscale the AML Endpoint: Set up autoscaling rules to dynamically adjust the number of instances based on real-time metrics, ensuring efficient handling of varying loads.​


### Essence of the steps via code/CLI commands: 

1. Authentication
```
az account set --subscription <subscription ID>
az configure --defaults workspace=<Azure Machine Learning workspace name> group=<resource group>
```
2. Build Environment
```
az ml environment create -f environment.yml
```
3. Deploy to Managed Online Endpoint
```
az ml online-endpoint create -f endpoint.yml
az ml online-deployment create -f deployment.yml --all-traffic
```
4. Get API endpoint and API keys
```
az ml online-endpoint show -n <name>
az ml online-endpoint get-credentials -n <name>
```
5. Test the model using the `test_model.py` file


## Acknowledgements

Special thanks to the following individuals for their invaluable contributions to this repo:

- Shankar Ramachandran: https://github.com/shankar-r10n
- Amy Xin: https://github.com/amyxixin
- Sherif Messiha: https://github.com/shmessiha
- Theresa Palayoor


## Contributing

This project welcomes contributions and suggestions.  Most contributions require you to agree to a
Contributor License Agreement (CLA) declaring that you have the right to, and actually do, grant us
the rights to use your contribution. For details, visit [Contributor License Agreements](https://cla.opensource.microsoft.com).

When you submit a pull request, a CLA bot will automatically determine whether you need to provide
a CLA and decorate the PR appropriately (e.g., status check, comment). Simply follow the instructions
provided by the bot. You will only need to do this once across all repos using our CLA.

This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/).
For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) or
contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any additional questions or comments.

## Trademarks

This project may contain trademarks or logos for projects, products, or services. Authorized use of Microsoft
trademarks or logos is subject to and must follow
[Microsoft's Trademark & Brand Guidelines](https://www.microsoft.com/legal/intellectualproperty/trademarks/usage/general).
Use of Microsoft trademarks or logos in modified versions of this project must not cause confusion or imply Microsoft sponsorship.
Any use of third-party trademarks or logos are subject to those third-party's policies.
