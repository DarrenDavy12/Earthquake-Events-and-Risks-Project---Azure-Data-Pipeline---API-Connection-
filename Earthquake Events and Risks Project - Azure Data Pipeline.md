# Earthquake Events and Risks Project - Azure Data Pipeline - API Connection

## **Summary:**

Ingested data using python within Databricks, to get it from a API endpoint into a bronze layer of an medallion architecture. Azure data lake storage container within a storage account. Then used Databricks to transform the raw data from bronze container into a silver container and then to the gold container to be used directly for business use. I have then brought these notebooks into Azure Data Factory, extracted variables out of the notebook into Data factory and orchestrated the pipeline from Data factory as well. 

## 1. Create databricks resource:

In Azure I made a Databricks workspace along with a resource group. 

This is a personal project so I choose **premium pricing tier** for my Databricks resource. 

Afterwards I reviewed and created the resource. 

![image.png](https://github.com/DarrenDavy12/Earthquake-Events-and-Risks-Project---Azure-Data-Pipeline---API-Connection-/blob/main/images/image.png?raw=true)

![image.png](https://github.com/DarrenDavy12/Earthquake-Events-and-Risks-Project---Azure-Data-Pipeline---API-Connection-/blob/main/images/image%201.png?raw=true)

![image.png](https://github.com/DarrenDavy12/Earthquake-Events-and-Risks-Project---Azure-Data-Pipeline---API-Connection-/blob/main/images/image%203.png?raw=true)

While the resource getting ready to deploy, I created a **storage account** to create my three **data containers “bronze, silver and gold”**  and also added a role assignment for access to the containers. 

## 2. Create and setup storage

In the basics tab → 

![image.png](https://github.com/DarrenDavy12/Earthquake-Events-and-Risks-Project---Azure-Data-Pipeline---API-Connection-/blob/main/images/image%204.png?raw=true)

On the advanced tab I **enabled hierarchical namespace** → 

![image.png](https://github.com/DarrenDavy12/Earthquake-Events-and-Risks-Project---Azure-Data-Pipeline---API-Connection-/blob/main/images/image%204.png?raw=true)

Afterwards I reviewed and created the storage account. 

![image.png](image%205.png)

Again I waited for the resource to deploy. 

![image.png](image%206.png)

![image.png](image%207.png)

*I also have another resource group managed by databricks when I created my databricks resource.* 

![image.png](image%208.png)

After the my storage account was created and deployed. I went into the containers tab on my storage account resource (on the left) 

![image.png](image%209.png)

![image.png](image%2010.png)

So here is where I created my bronze, silver gold container, by creating them one by one →

![image.png](image%2011.png)

![image.png](image%2012.png)

![image.png](image%2013.png)

Overview → 

![image.png](image%2014.png)

**Bronze layer** being **raw data for cleaning**, **silver layer** being the placeholder for that **clean data** and **finding the information we need for the business case** before moving that clean data to the **gold layer.**


## 3. Databricks configuration:

First I launched a workspace and started a **compute** instance which took a few minutes. 

![image.png](image%2019.png)

On the compute config, I selected single node, **unchecked Photon Acceleration** and choose the smallest node type which is **general purpose Standard_DS3-v2 (14 GB, 4 Cores).**

Set the self-**termination** of the node after **30 minutes** to be safe, just so any reason I am absent. Also it takes round 15 mins to spin up the Databricks cluster anyway, so lower than 25 mins is a risk. When I’ve done I clicked create compute then waited for the cluster to be created. 

![image.png](image%2020.png)

While the cluster is being created, I’d further configured my databricks resource by first creating a credential. 

**Catalog > External Data > Credentials > Create Credential**.

*Usually there is a credential there for default, but that is for the pipeline itself.* 

![image.png](image%2021.png)

I created a new credential for authentication on the storage account and Databricks cluster. 

**Credential type** = Azure managed identity 

**Credential name** = earthquakePipeline 

**Access connector ID** = search for “access connector” in azure search bar or find in my managed resource group identity located on my Databricks resource for this project. 

Name of connector in this case is: 

**unity-catalog-access-connector**

method 1: Search bar lookup 

![image.png](image%2022.png)

![image.png](image%2023.png)

method 2: Located on Databricks resource, go inside the link access it in the list below. 

![image.png](image%2024.png)

![image.png](image%2025.png)

![image.png](image%2026.png)

Once the id is copied to the clipboard, I paste it in the access connector id box inside the credential config.

Then I click create and noticed I now have two credentials, one of managed identity owned by Databricks and one owned by myself. 

![image.png](image%2027.png)

I wanted to use the credential for external locations 

**Catalog > External Data > External Locations > Create External location** 

![image.png](image%2028.png)

Created bronze, silver and gold external location. Each location must have a endpoint, to later access each container. 

Usually I write my URL in the comment section or on a notepad first to then paste it on the URL box. The storage credential I choose was the one new credential I just created. 

I did this for silver and gold too. 

![image.png](image%2029.png)

Once I clicked create, I came up against a error.

This is because my container is currently empty. 

So I selected **force update for all three of them**

![image.png](image%2030.png)

Overview once locations are created. 

![image.png](image%2031.png)

## 4. Create and execute Notebooks

So I head over to my storage account “earthquakedata” and go into **Access Control (IAM) > Role Assignments** 

I added a new Role Assignment 

![image.png](image%2032.png)

Searched and selected the “**storage blob contributor**” role. This role allows me to read, write and delete access to blob containers and data.

![image.png](image%2033.png)

On the next page called “**members**”, **I assigned access to managed identity** and added my access connector for Databricks “**unity-catalog-access-connector**”  as my selected member. 

Finally pasting in the resource id from me Databricks connector I used earlier in the select box. 

![image.png](image%2034.png)

![image.png](image%2035.png)

Overview 

Then I clicked review and assign. 

![image.png](image%2036.png)

I now have two-communication with my storage account and Databricks.

In workspace in Databricks, I created a notebook for each layer (gold, silver and bronze) 

Adding the relevant python code for the **bronze** notebook.

So I hit create new notebook 

![image.png](image%2037.png)

![image.png](image%2038.png)

*Plus Note:* 

Fetched the API for earthquakes events from this site:

[API Documentation - Earthquake Catalog](https://earthquake.usgs.gov/fdsnws/event/1/)

This will give all the information about the earthquakes seismic events. I added this to the URL variable inside the notebooks. This where the GET request will fetch the data from. 

![image.png](image%2039.png)

For each notebook I am them running isolated blocks. 

**Bronze** Notebook: 

Raw data ingested directly from the API, stored in Parquet format for future reprocessing if needed.

![image.png](image%2040.png)

![image.png](image%2041.png)

![image.png](image%2042.png)

**Silver** Notebook: 

Cleaned and normalized data, removing duplicates and handling missing values, ensuring it’s ready for analytics.

![image.png](image%2043.png)

![image.png](image%2044.png)

![image.png](image%2045.png)

![image.png](image%2046.png)

![image.png](image%2047.png)

Silver container inside my created storage account is no longer empty and now holds the data.

![image.png](image%2048.png)

### **Install Required Libraries:**

**Gold** Notebook: Aggregated and enriched data tailored to specific business needs, such as adding in country codes.

![image.png](image%2049.png)

I wanted to add the “reverse_geocoder” pypi package, so in 

**compute > [cluster_name] > libraries > install new**

I selected “pypi” package because I am work with python code, typed “reverse_geocoder” as my pyi package. 

Usually I wait 5 or so minutes for the package to be installed.  

![image.png](image%2050.png)

Once done 

![image.png](image%2051.png)

I went back to the **Gold** notebook and ran the cell including the “reverse_geocoder” package. 

![image.png](image%2052.png)

**Gold** notebook overview: 

![image.png](image%2053.png)

![image.png](image%2054.png)

![image.png](image%2055.png)

![image.png](image%2056.png)

Gold container inside my created storage account is no longer empty and now holds the data.

![image.png](image%2057.png)

## 5. **Set Up Azure Data Factory (ADF)**

Afterwards, I headed over to Data factories. 

![image.png](image%2058.png)

I created a new data factory 

On the basics tab: 

Resource Group: earthquake_data_pipeline

Name: “darrenearthquakedata” - had to be unique 

Region: **East US** 

Version: **V2**

Then I clicked review + create and create again after that. 

![image.png](image%2059.png)

![image.png](image%2060.png)

I am using data factory to run the python notebooks automatically based on triggers. 

*For example : “*at 12 am every night re-run the pipeline*”*

After the resource is deployed, I click “launch studio”

![image.png](image%2061.png)

![image.png](image%2062.png)

In the author tab on the left, this where I created the pipeline. 

![image.png](image%2063.png)

Dragged and dropped notebook onto the canvas. 

Renaming the notebook to “Bronze Notebook” in the general tab.

![image.png](image%2064.png)

***Plus Note:** Data factory communicates with Databricks not the storage account, but Databricks communicates with both.*  

On the Azure Databricks tab, I created a linked service with the configuration of “**AutoResolveIntegrationRuntime” and I was recommended to store the token in “Azure Key Vault” for security reasons.** 

**Linked service config:** 

Name of linked service: **AzureDatabricks1**

Connect Via integration runtime:  **AutoResolveIntegrationRuntime**

Account selection method: **Azure subscription** 

Databricks workspace: **earthquake-data_pipeline**

Select cluster: **Existing Databricks cluster** 

Choose existing cluster: **[account_name] cluster** 

I wanted a single access point for this project so I used a “access token”

*If I did use key vault I would need to assign my **IAM access identity** to assign a role (**secret officer role or secret user role**) to connect key vault to data factory.* 

**Location of access token:**

**Azure databricks > Profile icon > Settings > Developer > access tokens (manage) > generate new token**

![image.png](image%2065.png)

![image.png](image%2066.png)

Created the token and pasted it into the access token box back inside my data factory link service. 

![image.png](image%2067.png)

**Overview of new linked service:** 

![image.png](image%2068.png)

Tested the connection for validation and the connection was successful: 

![image.png](image%2069.png)

Next on the **settings tab > browse > users > [my outlook email] > Bronze Notebook**

Then I clicked “Ok”

![image.png](image%2070.png)

Cloned two more notebooks on the canvas. So I had three in total and rename two. One called Silver Notebook and the other Gold Notebook. 

![image.png](image%2071.png)

Then I changed the settings for both silver and gold notebook and set the corresponding folders: 

**Silver** Notebook:

**settings tab > browse > users > [my outlook email] > Silver Notebook** 

![image.png](image%2072.png)

and 

**Gold** Notebook: 

**settings tab > browse > users > [my outlook email] > Gold Notebook** 

![image.png](image%2073.png)

Once that was done, I connected the notebooks on the canvas using the “**On Success**” connector (green arrows) 

![image.png](image%2074.png)

I **validated** and **debugged** the pipeline for any errors and it was successful. 😎

![image.png](image%2075.png)

![image.png](image%2076.png)

Then I **published** the pipeline in order to add a trigger. 

![image.png](image%2077.png)

Clicked **publish** at the bottom. 

![image.png](image%2078.png)

Then I past variables from one notebook to another. 

So updated the code in the **Bronze** notebook: 

I put this code after importing requests and json. 

`dbutils.widgets.text("start_date", "")
dbutils.widgets.text("end_date", "")
start_date = dbutils.widgets.get("start_date")
end_date = dbutils.widgets.get("end_date")`

![image.png](image%2079.png)

I went back data factory and added two **base parameters**

**pipeline canvas > bronze notebook > settings > base parameters** 

One called “start_date” and the other “end_date” with both having **dynamic content** as the value. 

These date and time formats match the same format as the API. 

The **dynamic content** is for start_date is: 

`@formatDateTime(addDays(utcnow(), -1), 'yyyy-MM-dd')`

![image.png](image%2080.png)

The **dynamic content** is for end_date is: 

`@formatDateTime(utcnow(), 'yyyy-MM-dd')`

![image.png](image%2081.png)

Overview of **bronze** parameters: 

![image.png](image%2082.png)

Both start_date and end_date now have base parameters on the bronze notebook.

I headed back to bronze notebook in Databricks and add another cell at the bottom, this code will chain the **bronze** notebook to the other two notebooks, **silver** and **gold** → 

**Bronze** Notebook: 

![image.png](image%2083.png)

Back in the **silver** notebook I removed this cell of code. 

![image.png](image%2084.png)

Replaced that code with 

![image.png](image%2085.png)

Along with adding a output at the bottom 

![image.png](image%2086.png)

And on the **gold** notebook, I replaced this

![image.png](image%2087.png)

With this, allowing the Databricks to get the parameters from Data factory. 

![image.png](image%2088.png)

Back in Data factory, I add parameters for the **silver** notebook and the **gold** notebook. 

In the silver notebook parameters, I added “bronze_params” as the name and value being a dynamic content but this time using an activity output called “Bronze Notebook” 

![image.png](image%2089.png)

I altered the default activity output for the bronze notebook. 

![image.png](image%2090.png)

with this 

`@string(activity('Bronze Notebook').output.runOutput)`

![image.png](image%2091.png)

Then I did the same with the gold notebook, with bronze_params and silver_params

![image.png](image%2092.png)

Adding the dynamic content on silver_params as `@string(activity('Silver Notebook').output.runOutput)`

![image.png](image%2093.png)

And the same for bronze_params this the value being the bronze activity output `@string(activity('Bronze Notebook').output.runOutput)`

![image.png](image%2094.png)

All parameters are now passed through. 😎

Afterwards I published my pipeline. (top-left) 

![image.png](image%2095.png)

After publishing the pipeline, I ran a trigger:

**trigger > new/edit > choose trigger > new** 

name: Daily Trigger 

![image.png](image%2096.png)

After hitting “ok”, I published the pipeline again to publish that rigger I just created. 

![image.png](image%2097.png)

I ran the trigger there and the to see if the trigger is work and I got this prompt in the top-right saying “Running” which means the trigger was successful. 

![image.png](image%2098.png)

On the prompt which popped up, I click on “View pipeline run” which is a link which sent me to the **monitor** tab in Data factory. The scheduled/Triggered pipeline is now complete. 😎

![image.png](image%2099.png)
