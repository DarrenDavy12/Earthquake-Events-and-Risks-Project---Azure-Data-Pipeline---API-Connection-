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


Creating Containers
Navigate to the containers section in the storage account.
Create three containers: bronze (raw data for cleaning), silver (clean data for analytics), and gold (enriched data for business use).
Overview of created containers.

![Image](https://github.com/user-attachments/assets/e691d321-92ed-4187-bbc8-3ae6d672c409)


![Image](https://github.com/user-attachments/assets/689e24ec-111b-4abd-81fb-7e79585e811b)


![Image](https://github.com/user-attachments/assets/fbdbda5d-0d96-494d-8fae-abed2e5e3efb)


![Image](https://github.com/user-attachments/assets/e4f1b067-a86a-40b4-8e74-3f6f7039d86c)


![Image](https://github.com/user-attachments/assets/589c257e-5147-41c5-aaeb-0925d425365a)

![Image](https://github.com/user-attachments/assets/e5075bc7-d6b1-483c-9fd6-6a2f6441569c)



Setting Up Synapse Analytics
Create a Synapse Analytics workspace, choosing the storage account created earlier.
Create a file system named "synapse-fs" and assign "Storage Blob Data Contributor" role.
In the security tab, select only Entra ID for authentication.
Review and create, then wait for deployment.


![Image](https://github.com/user-attachments/assets/29d081d1-82ee-41d0-b7d9-d4ee9d5f4811)


![Image](https://github.com/user-attachments/assets/778c0e50-7cd4-47e6-852a-f2abc8e3cabe)


![Image](https://github.com/user-attachments/assets/9ceb7368-0062-46d5-bc93-d3e5cd4fffd0)


![Image](https://github.com/user-attachments/assets/7a2c463e-8597-4175-b093-5ec55f338308)



Databricks Configuration
This section details configuring the Databricks environment for data processing, including launching compute instances, creating credentials, and setting up external locations.

Launching Compute Instance
Launch the Databricks workspace and start a compute instance.
Select single node, uncheck Photon Acceleration, choose Standard_DS3-v2 (14 GB, 4 Cores).
Set self-termination after 30 minutes to avoid unnecessary costs, considering cluster spin-up time is around 15 minutes.
Wait for the cluster to be created.


![Image](https://github.com/user-attachments/assets/a75c4a61-f6a5-43ce-8ec7-fabbcee32e83)


![Image](https://github.com/user-attachments/assets/377e837d-afcd-450c-beaa-742649a336fb)


Creating Credential
Navigate to Catalog > External Data > Credentials > Create Credential.
Choose Azure managed identity, name it "earthquakePipeline".
Find the access connector ID, either by searching in Azure or locating in the Databricks resource under managed identities.
Paste the ID and create, verifying two credentials are present (default and new).



![Image](https://github.com/user-attachments/assets/62c24448-aa7e-43d4-b7f4-c4d0b6c4f990)


![Image](https://github.com/user-attachments/assets/b31f0ac6-1c8b-4e3a-9ebd-9aa09f74e3a2)


![Image](https://github.com/user-attachments/assets/4109d8c1-548b-4e88-9a74-d3c7719deffc)

![Image](https://github.com/user-attachments/assets/63a8c115-02fb-4fca-8769-b87d910d65a5)


![Image](https://github.com/user-attachments/assets/8c11a6b9-c3bf-4dec-8aaf-0238e93fb018)


![Image](https://github.com/user-attachments/assets/be7b4ba8-799b-4948-83d4-0f27562b0a22)


Creating External Locations
Navigate to Catalog > External Data > External Locations > Create External Location.
Create locations for bronze, silver, and gold, using the URL for each container and the new credential.
Initially, encounter an error due to empty containers, force update for all.
Overview of created external locations.


![Image](https://github.com/user-attachments/assets/eb636944-642c-48ba-a9cb-19bf58cd11c1)


![Image](https://github.com/user-attachments/assets/99e02879-4cdb-46c8-98ed-3ee0614bcd56)


![Image](https://github.com/user-attachments/assets/d8c24306-32eb-40a0-bdaf-7c859f5738af)


![Image](https://github.com/user-attachments/assets/2a8f4105-4e18-40f8-8362-f25d659ac2d2)



Notebooks Creation and Execution
This section covers setting up access for the storage account and creating notebooks for each layer of the medallion architecture.

Setting Up Access for Storage Account
In the storage account, go to Access Control (IAM) > Role Assignments.
Add a new role assignment for "Storage Blob Contributor", allowing read, write, and delete access to blob containers.
Assign to managed identity "unity-catalog-access-connector", pasting the resource ID from Databricks connector.
Review and assign, ensuring two communications are set up.



![Image](https://github.com/user-attachments/assets/afba8bf1-231b-4be8-bffb-01aef38c4ee5)


![Image](https://github.com/user-attachments/assets/fe610b1c-5389-47f5-8a99-82fd2eb433be)


![Image](https://github.com/user-attachments/assets/cad399ab-079e-4c82-8b1b-9a5c5d3f2a4e)


![Image](https://github.com/user-attachments/assets/2409bc66-7841-4b7f-abf9-02ad9380cd66)


![Image](https://github.com/user-attachments/assets/9eea238c-52ac-4276-821f-85556f9eab0c)

