# Azure Data Pipeline - (Databricks - Azure Data Factory - Azure Data Lake - Azure Blob Storage - Python) - Earthquake Events and Risks Project 



Ingested data using python within Databricks, to get it from a API endpoint into a bronze layer of an medallion architecture. Azure data lake storage container within a storage account. Then used Databricks to transform the raw data from bronze container into a silver container and then to the gold container to be used directly for business use. I have then brought these notebooks into Azure Data Factory, extracted variables out of the notebook into Data factory and orchestrated the pipeline from Data factory as well.

Resource Setup
This section covers the initial setup of necessary Azure resources, divided into creating Databricks workspace, setting up storage account, creating containers, and setting up Synapse Analytics.

Creating Databricks Workspace
In Azure, create a new Databricks workspace, choosing the premium pricing tier for this personal project.
Review and create the resource, then wait for deployment.

![Image](https://github.com/user-attachments/assets/5b999166-7e7e-4602-8194-310fdcafed36)

![Image](https://github.com/user-attachments/assets/49e62521-db0e-4377-b58c-be71438d3ab3)

![Image](https://github.com/user-attachments/assets/8d179614-9ae2-4c37-8ac0-d1c39252f4db)




Setting Up Storage Account
Create a storage account while the Databricks workspace is deploying.
In the basics tab, configure settings, and in the advanced tab, enable hierarchical namespace.
Review and create, then wait for deployment.
Note the additional resource group managed by Databricks.

![Image](https://github.com/user-attachments/assets/328ac9eb-8720-4e4f-ba8b-3de4cde5f9e6)


![Image](https://github.com/user-attachments/assets/9261e1fc-5ce7-4b80-ac34-abaf69b71920)


![Image](https://github.com/user-attachments/assets/d0cab7d0-5196-4970-bbf0-4d93fb9a7a47)


![Image](https://github.com/user-attachments/assets/25acdfc6-2028-4f5c-bbf2-50f64a1e64bb)


![Image](https://github.com/user-attachments/assets/a357434c-6182-443b-86e1-b78a3d3c4430)


![Image](https://github.com/user-attachments/assets/d31be78a-a5e3-4851-ade3-bb443995d67a)


![Image](https://github.com/user-attachments/assets/e691d321-92ed-4187-bbc8-3ae6d672c409)


![Image](https://github.com/user-attachments/assets/689e24ec-111b-4abd-81fb-7e79585e811b)


![Image](https://github.com/user-attachments/assets/fbdbda5d-0d96-494d-8fae-abed2e5e3efb)


![Image](https://github.com/user-attachments/assets/e4f1b067-a86a-40b4-8e74-3f6f7039d86c)


