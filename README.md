IBM Cloud Pak for Integration (CP4I) Practicum Lab Book for Scenario 3:
=================
This Practicum Lab Book explains and focuses on the solution preparation and implementation for Practicum Scenario 3 using Cloud Pak for Integration (CP4I). The book will cover the solution architecture and the details of how to best implement the solution for the given scenario. This will help accelerate adoption of Cloud Pak for Integration.

Table of contents
=================

<!--ts-->
   * [Topic 1: Introduction and Scenario Details](#topic-1-introduction-and-scenario-details)
   * [Topic 2: Introduction to Clusters and Openshift](#topic-2-introduction-to-clusters-and-openshift)
   * [Topic 3: Solution Architecture](#topic-3-solution-architecture)
   * [Topic 4: Running Transformation Advisor](#topic-4-running-transformation-advisor)
      * [Installing IBM App Connect Enterprise (ACE) & Running Transformation Advisor (TADataCollector)](#installing-ibm-app-connect-enterprise-ace--running-transformation-advisor-tadatacollector)
   * [Topic 5: Environment Setup and Configuration](#topic-5-environment-setup-and-configuration)
      * [Provisioning Cluster](#provisioning-cluster)
      * [Creating Projects](#creating-projects)
      * [Installing Cloud Pak for Integration on Openshift](#installing-cloud-pak-for-integration-on-openshift)
      * [Creating Platform Navigator](#creating-platform-navigator)
      * [Creating Integration Dashboard](#creating-integration-dashboard)
      * [Creating MQ Queue Manager](#creating-mq-queue-manager)
   * [Topic 6: Refactor, Build and Deployment](#topic-6--refactor-build-and-deployment)
      * [Importing Asset into IBM ACE Toolkit and creating local integration server](#importing-asset-into-ibm-ace-toolkit-and-creating-local-integration-server)
      * [Refactor ACE REST to REST Message flow](#refactor-ace-rest-to-rest-message-flow)
      * [Refactor ACE MQ Message flow using MQ Client Connection.](#refactor-ace-mq-message-flow-using-mq-client-connection)
   * [Topic 7: Conclusion](#topic-7-conclusion)
<!--te-->

&nbsp;
&nbsp;
&nbsp;
&nbsp;
&nbsp;
&nbsp;
# Topic 1: Introduction and Scenario Details
Your customer has been a long-time user of **App Connect (IIB/WMB)** and **MQ**. They have recently decided to investigate the value of modernizing their integration. They are eager to explore IBM Agile Integration practices. They would like you to use one of their existing flows and queue definitions to explore how to move to containerized integration.

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/scenario3-image1.png?raw=true)

While eager to understand the modernization journey, they also have reservations on their ability to scale and manage the workload.  Demonstrate how to analyze, componentize, and containerize existing integration artefacts, and illustrate how to scale and monitor the new deployments.

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/scenario3-image2.png?raw=true)



**Your Challenge**

Your team have (5) days to document your approach to a Solution to the customer’s business problem using CP4I capabilities and any external capabilities that you think are necessary or useful.
Demonstrate your practical solution to whole class.

Day 5: Demonstrate your practical solution to whole class.

[Back to Top](#table-of-contents)

&nbsp;
&nbsp;
&nbsp;
&nbsp;
&nbsp;
&nbsp;


# Topic 2: Introduction to Clusters and Openshift

## Clusters and Openshift

### Introduction to Cluster

If you're completely new to the concept of clusters, do follow the quick and easy tutorial [here](https://www.redhat.com/en/technologies/cloud-computing/openshift) to learn more
> With Red Hat® OpenShift® on IBM Cloud®, you can create highly available clusters with virtual or bare metal worker nodes that come installed with the Red Hat OpenShift on IBM Cloud Container Platform orchestration software. You get all the advantages of a managed offering for your cluster infrastructure environment, while using the Red Hat OpenShift tooling and catalog that runs on Red Hat Enterprise Linux for your app deployments.


### Introduction to Openshift

OpenShift is a platform that allows you to run containerized applications and workloads and is powered by Kubernetes. It is an offering that comes with Red Hat support, regardless of where you choose to run your applications and workloads. 

One of the big advantages of OpenShift is being able to take advantage of public and private resources which includes bare metal or virtualized hardware whether it is on-premise or on a cloud provider. 

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/openshift-overview.png?raw=true)

This is the high level OpenShift Container Platform overview.

For developers, OpenShift has two different ways of enabling them to work with their platform. They can take advantage of either the CLI or a web console. 

[Back to Top](#table-of-contents)

&nbsp;
&nbsp;
&nbsp;
&nbsp;
&nbsp;
&nbsp;


# Topic 3: Solution Architecture
Below describe the high-level solution architecture for a simple scenario's solution.
By using IBM IIB/ACE transformation advisor's TADataCollector, we can analyse the current monolith IIB/ACE application. A report will be generated to recommend what are needed to refactor, in order to achieve a microservice architecture build.

The refactor code can later be deployed in a microservices architecture on Redhat Openshift with CP4I (ACE + MQ) environment 

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/scenario3-architecture.png?raw=true)


[Back to Top](#table-of-contents)


&nbsp;
&nbsp;
&nbsp;
&nbsp;
&nbsp;
&nbsp;



# Topic 4: Running Transformation Advisor

## Installing IBM App Connect Enterprise (ACE) & Running Transformation Advisor (TADataCollector)

1.	Install IBM App Connect Enterprise for developers (also called ACE). Make sure to select the correct download package for your OS (Windows, Linux, Mac).
Click on "Download" button on the following link:  https://www.ibm.com/docs/en/app-connect/12.0?topic=enterprise-download-ace-developer-edition-get-started 
The version used in this practicum is `12.0.3`

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/AppConnect1.png?raw=true)

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/AppConnect2.png?raw=true)

   Complete the installation through the installer package you just downloaded for your OS.  eg: `12.0.3.0-ACE-MAC64-DEVELOPER-UNSIGNED`. You can use the IBM ACE Installation page as a guide to complete the installation.


2. Once installed, open the installed ACE toolkit. A view similar to the screenshot below will launch.

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/AppConnect3.png?raw=true)

3. Open the ```Integration Console``` by **IBM App connect Enterprise Toolkit** -> **Open Integration console**

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/AppConnect4.png?raw=true)

4. IBM ACE console will launch on your screen as below:

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/AppConnect5.png?raw=true)

5. Run the Transformation Advisor by running `TADataCollector` on this console and passing Scenario Assets-IIB ```IIBV10_Broker_backup.zip``` file through command line, as shown below: 

```
TADataCollector ace run /<path_to_assets_on_local_machine>/IIBV10_Broker_backup.zip
```

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/AppConnect6.png?raw=true)

6. Once the `TADataCollector` command runs successfully, it will generate recommendation files under `/tmp/TADataCollector/output/IIB1` folder. Open the following directory to access the generated files

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/AppConnect7.png?raw=true)

7.	Open the recommendations.html file located in `/tmp/TADataCollector/output/IIB1` folder & Read the recommendations made by IBM Transformation Advisor

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/AppConnect8.png?raw=true)

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/AppConnect9.png?raw=true)


[Back to Top](#table-of-contents)

&nbsp;
&nbsp;
&nbsp;
&nbsp;
&nbsp;
# Topic 5: Environment Setup and Configuration

## Provisioning Cluster

The steps outlined below will assist you to prepare cluster for the installation of Cloud Pak for Integration. This section of the document contains step by step process with guiding screenshots for the cluster install.

1. Login to TechZone (https://techzone.ibm.com )

2. Create ROKS Cluster as a pre-requisite to install Cloud Pak of Integration --> Click Reserve to reserve the cluster. 
   Below snapshot can be used for reference – 

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/CI1.png?raw=true)

3. You will be prompted to select one option from below, select “Reserve Now”.

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/CI2.png?raw=true)

4. Select one of the choices below as the Purpose of Reservation.

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/CI3.png?raw=true)

5. Describe the purpose and select the geography in which you want to create the cluster

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/CI4.png?raw=true)

6. Enter the date and time until which you may require the cluster. It can be reserved only for 2 weeks. Enter the notes if any and click “Submit”

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/CI5.png?raw=true)

7. Upon successful creation, you would see the below screen.

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/CI6.png?raw=true)

8. Select “My Reservations” tab --> Cluster should be listed here

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/CI7.png?raw=true)

9. Click on the Reservations and Open RedHat Open Shift Platform console. 
   It will have following details – 
   a. Cluster API Address
   b. Cluster ID
   c. Provider
   d. Version

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/CI8.png?raw=true)

**Adding catalog sources to an online OpenShift cluster
**

10. Adding the IBM operator catalog to your OpenShift cluster adds the IBM operators to the list of operators you can install. On the top right banner, click the {+} icon to open Import YAML dialogue box. 

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/CI9.png?raw=true)

11. Paste below resource definition into the dialogue box  and click “Create” – 
```
apiVersion: operators.coreos.com/v1alpha1
kind: CatalogSource
metadata:
  name: ibm-operator-catalog
  namespace: openshift-marketplace
spec:
  displayName: IBM Operator Catalog
  image: 'icr.io/cpopen/ibm-operator-catalog:latest'
  publisher: IBM
  sourceType: grpc
  updateStrategy:
    registryPoll:
      interval: 45m
```

## Creating Projects 

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/createproject1.png?raw=true)

Head to the Red Hat OpenShift Web Console, ensure that you are on Administrator view. Click on "Projects" in the left panel. On the top right hand corner, you will see the button to "Create Project" (encircled in black).


![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/createproject2.png?raw=true)

Fill in the details accordingly and hit "Create". 

## Installing Cloud Pak for Integration on Openshift

### Obtaining and applying Entitlement Key

The IBM Entitled Registry contains software images for the capabilities in IBM Cloud Pak® for Integration. To allow the Cloud Pak for Integration operators to automatically pull those software images, you must first obtain your entitlement key, then add your entitlement key in a pull secret.

1. Obtain your entitlement key using the url - https://myibm.ibm.com/products-services/containerlibrary

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/EntKey1.png?raw=true)

2.	Your entitlement key must be added to the cluster as a pull secret to deploy Cloud Pak for Integration capabilities. You can either add a pull secret to each namespace you plan to use for Cloud Pak for Integration capabilities, or add a global pull secret, which enables deployment of Cloud Pak for Integration capabilities in all namespaces.	
Navigation --> Open Shift Container Platform --> Workloads --> Secrets --> On the top left you can see “Create” --> Click and select “Image Pull Secret” (**Ensure it’s your custom namespace under your project**)


![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/EntKey2.png?raw=true)

3. Add the details as shown in the snapshot below. In “Password” enter the entitlement key copied in Step – 1 and click “Create” the secret key.

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/EntKey3.png?raw=true)

4. Upon creation it will appear under your project.

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/EntKey4.png?raw=true)

This section completes the configuration of Entitlement Key.

[Back to Top](#table-of-contents)

### Installing Cloud Pak for Integration Operators

1. Login to Red Hat Open Shift Console --> Navigate to Operators --> Operator Hub --> Search “IBM Cloud Pak for Integration”

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/IntInstall1.png?raw=true)

2. Select and click “Install”

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/IntInstall2.png?raw=true)

3. Ensure to choose your namespace under your project at the time of installation

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/IntInstall3.png?raw=true)

4. Upon successful installation below message can be seen. Click on View Operator to gather more details on the installed operator.

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/IntInstall4.png?raw=true)

5. Click View Operator and scroll down to the end of page to validate status, version and namespace

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/IntInstall5.png?raw=true)

This completes the installation of IBM Cloud Pak for Integration All Operators


[Back to Top](#table-of-contents)

&nbsp;
## Creating Platform Navigator

1. Login to Red Hat Open Shift Cluster and navigate to Operators --> Installed Operators and search for “Platform Navigator”

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/PlatformNavigator1.png?raw=true)
  
2. Click and navigate to “Platform Navigator” tab and click “Create PlatformNavigator

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/PlatformNavigator2.png?raw=true)
  
3. Click Create PlatformNavigator. The Create Platform Navigator panel opens, and offers two methods for configuring the resource; the Form viewand the YAML view. The Form view is selected by default:

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/PlatformNavigator3.png?raw=true)


[Back to Top](#table-of-contents)

## Configuring in the Form view
The Form view opens a form that lets you view or modify the resource configuration.
Note: Some fields may not be represented in the default form view.

4. Change Project to your project (namespace) name. Click the drop-down arrow and select your project name from the list. 
   The project name in the example is cp4i-demo. In the Name field, enter a name for the new instance or leave the default.
   
![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/PlatformNavigator4.png?raw=true)
  
5. Next to License, click the arrow to expand the license acceptance section. 
    a. Set License Accept to true if you accept the Cloud Pak for Integration license agreement.
    b. For License LI, click the arrow to open the drop-down list, and select a license.

 
![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/PlatformNavigator5.png?raw=true)
  

6. Specify the Storage class. Click the arrow to expand the Storage pane, then click the arrow for Select Storage Class and select a file storage class a file storage class that supports ReadWriteMany (RWX) volumes and allows read and write access to non-root users. 
   
   
![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/PlatformNavigator6.png?raw=true)
   
   
   
   
Supported storage providers include ibmc-file-gold-gid, OpenShift Data Foundation (formerly OpenShift Container Storage), Spectrum, and Portworx.
 

7. Set any other configuration values as appropriate and click Create.


![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/PlatformNavigator7.png?raw=true)


[Back to Top](#table-of-contents)


&nbsp;
## Creating Integration Dashboard

1. Go to Platform Navigator and click on the **Hamburger on top left** --> **Administration** --> **Integration Instances**

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/PlatformNavigator.png?raw=true)
 
2. Click **Create Instance** on top left of the page

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/CreateInstance-AppConnect.png?raw=true)
 
3. Select **Integration Dashboard** and click next

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/IntegrationDashboard.png?raw=true)
 
4. Select either **Quick Start** --> click next

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/IntegrationDashboardType.png?raw=true)
 
5. Key-in the details of the environment as below and proceed to create the dashboard.

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/IntegrationDashboard-Env.png?raw=true)
 
6. Upon successful creation of Integration Dashboard below message will appear on the browser. 

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/IntegrationDashboard-Final.png?raw=true)


[Back to Top](#table-of-contents)

&nbsp;
## Creating MQ Queue Manager

1. Go to IBM Cloud Pak Home, click on “Messaging” as highlighted in the screen below

1. Go to Platform Navigator and click on the **Hamburger on top left** --> **Administration** --> **Integration Instances**

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/PlatformNavigator.png?raw=true)
 
2. Click **Create Instance** on top left of the page

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/CreateInstance-AppConnect.png?raw=true)
 
3. Select **Messaging** and click next

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/IntegrationDashboard2.jpg?raw=true)

3. Select **Quick start** option from this screen, and click on Next.

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/CreateQMngr.png?raw=true)
 
4. Modify the details for your queue manager as below:
   
   a. **License acceptance** by toggling the button 


   ![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/Licence.png?raw=true)
 
 
 
   b. Select **Type of availability** from dropdown as **SingleInstance**  
 
   
  ![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/AvailabilityType.png?raw=true)
 
 
   c. Select **Type of Volume** from the drop down as **persistent-claim**
 
  ![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/VolumeType.png?raw=true)
 
 
 5. Lastly click on **Create** from the top right corner and queue will be created. 
   You will be redirected to a new page, showing the details of your newly created Queue manager.
   
 ![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/QMngrReady.png?raw=true)
 

6. Click on queue manager name --> It will open up MQ Console

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/MQConsole.png?raw=true)
 
7. Create ```IN``` and ```OUT``` MQ queues
   
   a. Click **Create a queue**
![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/createq1.jpg?raw=true)

   b. Click **Local**
![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/createq2.jpg?raw=true)

   c. Input **Queue Name** ```IN``` as below and click **Create**
![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/createq3.jpg?raw=true)

   d. Repeat step **a** to **c** with by creating ```OUT``` MQ queue
 
7. Click on  **Manage QUICKSTART** to open queue manager to view the queue you have created

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/createq4.jpg?raw=true)
 

 Your MQ environment is **ready!!!**
 
 [Back to Top](#table-of-contents)
 
&nbsp;
&nbsp;
&nbsp;
&nbsp;
&nbsp;
&nbsp; 
# Topic 6 : Refactor, Build and Deployment

&nbsp; 
## Importing Asset into IBM ACE Toolkit and creating local integration server
1. Import a project in ACE by navigating **File -> Import -> Project Interchange**

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/HTTP1.png?raw=true)

2. Browse on your local machine to add the **MessageFlow_PI.zip** file from the Scenario 3 assets as shown below

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/HTTP2.png?raw=true)

3. Import the artifact into ACE as shown in screenshot below - 

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/HTTP3.png?raw=true)

4. Click on finish to import the Project. Your assets are successfully imported.

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/HTTP4.png?raw=true)

   This will open child window to enter details of the server. Once completed, hit finish.
![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/HTTP5.png?raw=true)



[Back to Top](#table-of-contents)   

&nbsp; 
## Refactor ACE REST to REST Message flow

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/scenario3-application-architecture-ace-rest-rest.png?raw=true)

## HTTPResponseApp

1. Generate BAR file for the assets. Right click **BAR --> New --> BAR File** 
   Add a name (**HttpResponseApp**) for the BAR file and click    Finish.

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/HTTP8.png?raw=true)

2. Select resources to include in the BAR file (**HttpResponseApp**), then click on **Build and Save** to generate BAR file. 

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/HTTP9.png?raw=true)


3. Once Build, the BAR file will appear under BARs on the left side of Application Development panel. 


 4. Navigate to IBM Cloud Pak for Integrations integration dashboard you create earlier and select **Create a server**

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/IBMAppConnect.png?raw=true)
 
 5. Select **QuickStart tool kit integration** --> Next

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/DeployBAR.png?raw=true)
 
 6. Provide the BAR file to be deployed to the server and hit next.
 
   >Bar file can be directly drag and drop onto CP4I BAR file upload section

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/CreateIntServer3.jpg?raw=true)
 
 7. Skip **Configuration** setting by clicking **Next** to proceed to **Server** settings. Input the settings as shown in the screenshot below.

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/CreateIntServer4.jpg?raw=true)
 
 8. Click **Create** and wait until the status of the server has changed to "Ready"

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/IntServerStatus.png?raw=true)


## HTTPRequestApp
1. Open ACE Toolkit and navigate to **HttpRequestApp —> Expand to open folder Flows —> RequestService.msgflow**

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/refactor-http-1.png?raw=true)

2. Proceed to make the following changes to each component as below:

   a. Select **HTTP Request** on the messageflow. 
	Change the path **Web service URL** to the **URL obtain in following steps b**

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/ChangeProperties1.jpg?raw=true)
  
  
   b. Open **OCP Console --> Administrator View --> Networking -->  Routes --> Search for** ```response```

   copy **http** URL in the **Location** column.
   
![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/refactor-http-2.png?raw=true)

   Replacing HTTP request **Web service URL** http://localhost:7800/responseService  with ```[Copied URL]/responseService``` and **save**
  
![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/refactor-http-3.png?raw=true)
  
3. Create a BAR file for this flow and deploy it to a new Integration Server in OCP.

To generate BAR file for the assets. Right click **BAR --> New --> BAR File**. 
   Add a name (**HttpRequestApp**) for the BAR file and click    Finish.

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/HTTP8.png?raw=true)

4. Select resources to include in the BAR file (**HttpRequestApp**), then click on **Build and Save** to generate BAR file. 

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/CreateIntServer6.jpg?raw=true)


5. Once Build, the BAR file will appear under BARs on the left side of Application Development panel. 


 6. Navigate to IBM Cloud Pak for Integrations integration dashboard you create earlier

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/IBMAppConnect.png?raw=true)
 
 7. Create new Integration Server to deploy BAR file for MQ by selecting **QuickStart tool kit integration --> Next**

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/DeployBAR.png?raw=true)
 
 8. Provide the BAR file to be deployed to the server and hit next.
 
 >Bar file can be directly drag and drop onto CP4I BAR file upload section

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/HTTP11.jpg?raw=true)
 
 9. Skip **Configuration** screen by clicking **Next** to proceeding to **Sever** setting. Input the settings as shown in the screenshot below.

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/CreateIntServer5.jpg?raw=true)
 
 10. Click **Create** and wait until the status of the server has changed to "Ready"

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/IntServerReadyStatus.png?raw=true)


11. Test the deployed BARs
   
   a. open **OCP Console --> Administrator View --> Networking -->  Routes --> Search for** ```request```

   ![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/HTTPRequestTest.jpg?raw=true)

   b. Copy URL

   c. Open Terminal on your local machine

   d. Use curl command to test your deployment 

```
curl -v [Copied URL]/requestService
```

12. Receiving 200 OK on the terminal validates successful deployment

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/refactor-http-5.png?raw=true)

[Back to Top](#table-of-contents)

&nbsp; 
## Refactor ACE MQ Message flow using MQ Client Connection.

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/scenario3-application-architecture-ace-mq.jpg?raw=true)


## MQ_Client_App

1. Obtain MQ Endpoint, login to your cluster and navigate to **Networking --> Services --> Search** ```MQ```. Copy **Hostname** and **Port** as highlighted

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/HostName.png?raw=true)
 
 
2. Navigate to ACE Toolkit and open **RemoteMQInOut.msgflow** flow in **MQ_CLIENT_APP** as like below
 
![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/ACE-MQFlow.png?raw=true)
 

3. Select **MQ Input** Node
    
![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/ACEMQ2.png?raw=true)
 
 
4. Change properties to

| Properties | Value |
| ------------- | ------------- |
| Connection  | MQ Client Connection Properties  |
| Destination Queue Manager Name  | QUICKSTART  |
|Queue Manager Host Name | Value retrieved in step 1 |
|Listener Port | Value retrieved in step 1 |
|Channel name | SYSTEM.DEF.SVRCONN |

Then **save**.

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/MQClientInputNode.jpg?raw=true)


5. Repeat the same configuration for **MQ Output** Node

| Properties | Value |
| ------------- | ------------- |
| Connection  | MQ Client Connection Properties  |
| Destination Queue Manager Name  | QUICKSTART  |
|Queue Manager Host Name | Value retrieved in step 1 |
|Listener Port | Value retrieved in step 1 |
|Channel name | SYSTEM.DEF.SVRCONN |

Then **save**.

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/MQClientOutputNode.jpg?raw=true)



6. To generate BAR file for the assets. Select **BAR --> New --> BAR File**. 
   Add a name (**MQ_CLIENT_APP**) for the BAR file and click    Finish.

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/HTTP8.png?raw=true)

7. Select resources to include in the BAR file (**MQ_CLIENT_APP**), then click on **Build and Save** to generate BAR file. 


![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/mqClientBAR.jpg?raw=true)


8. Once Build, the BAR file will appear under BARs on the left side of Application Development panel. 



9. Navigate to IBM Cloud Pak for Integrations integration dashboard

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/IBMAppConnect.png?raw=true)
 
 10. Create new Integration Server to deploy BAR file by selecting **QuickStart tool kit integration**. Then click **Next**

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/DeployBAR.png?raw=true)
 
 11. Provide the BAR file to be deployed to the server and hit next.
 
 >Bar file can be directly drag and drop onto CP4I upload BAR section

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/CreateIntServer.png?raw=true)
 
 12. Skip the **Configuration** by clicking **Next** to proceeding to **Server** settings. Input the settings as shown in the screenshot below.

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/CreateIntServer2.png?raw=true)
 
 13. Hit "Create" and wait until the status of the server has changed to "Ready"

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/IntServerStatus.png?raw=true)
 
 14. The status changes to "Ready"

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/IntServerReadyStatus.png?raw=true)

## RestToMQApp

1. Obtain MQ Endpoint, login to your cluster and navigate to **Networking --> Services --> Search** ```MQ```. Copy Hostname and Port as highlighted

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/HostName.png?raw=true)
 
 
2. Navigate to ACE Toolkit and open **postAccount.msgflow** flow in **RESTToMQApp** as like below
 
![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/resttomqapp1.jpg?raw=true)
 

3. Select **MQ Output** Node
     
4. Change MQ Connection properties to

| Properties | Value |
| ------------- | ------------- |
| Connection  | MQ Client Connection Properties  |
| Destination Queue Manager Name  | QUICKSTART  |
|Queue Manager Host Name | Value retrieved in step 1 |
|Listener Port | Value retrieved in step 1 |
|Channel name | SYSTEM.DEF.SVRCONN |

Then **save**.

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/resttomqapp2.jpg?raw=true)

5. To generate BAR file for the assets. Select BAR --> New --> BAR File 
   Add a name (**RESTToMQApp**) for the BAR file and click **Finish**.

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/HTTP8.png?raw=true)

6. Select resources to include in the BAR file (**RESTToMQApp**), then click on **Build and Save** to generate BAR file. 


![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/resttomqbar.jpg?raw=true)


7. Once Build, the BAR file will appear under BARs on the left side of Application Development panel. 


8. Navigate to IBM Cloud Pak for Integrations integration dashboard

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/IBMAppConnect.png?raw=true)
 
9. Create new Integration Server to deploy BAR file by selecting **QuickStart tool kit integration**. Then click **Next**

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/DeployBAR.png?raw=true)
 
10. Provide the BAR file to be deployed to the server and hit next.
 
 >Bar file can be directly drag and drop onto CP4I upload BAR section

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/resttomqbardeploy.jpg?raw=true)
 
11. Skip the **Configuration** by clicking **Next** to proceeding to **Server** settings. Input the settings as shown in the screenshot below.

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/resttomqbardeploy2.jpg?raw=true)
 
12. Click **Create** and wait until the status of the server has changed to "Ready"

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/IntServerStatus.png?raw=true)
 
13. The status changes to "Ready"

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/ready.jpg?raw=true)


## Running mq_ace_lab.mqsc

There are multiple layers of security for MQ such as authorization and authentication configured on the Channel access. To simplify this exercise, we will disable all security with an MQ Script. Below steps will assist to disable - 

**Pre-requisite**

Install Openshift CLI client for your system by following the steps outlined in the below documentation

https://docs.openshift.com/container-platform/4.10/cli_reference/openshift_cli/getting-started-cli.html
 



1. Login to Openshift CLuster Environment. Click on the top right corner --> You Login ID will appear here --> Click copy login command


![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/MQAC2.png?raw=true)

2. Browser will display "Display Token" --> Click to get your API Token
   
![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/MQAC3.png?raw=true)


![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/MQAC4.png?raw=true)
   
3. Copy the command captured in the previous step and use it to connect to CLI


![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/MQAC6.png?raw=true)


4. Navigate to your working project example - cp4i in this example


```
oc project cp4i
```


![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/MQAC7.png?raw=true)

5. Run command as below to get the pod name of pod running MQ. Copy the pod name to be used later.

```
oc get pods|grep mq 
```

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/MQAC8.png?raw=true)

6. Change Directory to the location of your mqsc file. Use the following command to upload mqsc file to the MQ pod

```
oc exec -it quickstart-cp4i-ibm-mq-0 runmqsc QUICKSTART < mq_ace_lab.mqsc
```

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/MQAC9.png?raw=true)

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/MQAC10.png?raw=true)


## Testing RESTToMQApp

 1. Navigate to your Integration Dashboard and click on **RESTToMQApp**   

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/resttomqtest1.jpg?raw=true)

2. Click on **POST /account**

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/resttomqtest2.jpg?raw=true)

3. Click **Try it**
![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/resttomqtest3.jpg?raw=true)

3. Generate some test data by clicking **Generate**

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/resttomqtest4.jpg?raw=true)

4. Click **Send**

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/resttomqtest5.jpg?raw=true)

5. REST response result will display as below

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/resttomqtest6.jpg?raw=true)

6. Validate an output message is created in MQ ```OUT``` queue 

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/resttomqtest7.jpg?raw=true)

[Back to Top](#table-of-contents)     
    
    
# Topic 7: Conclusion  
 
 The above completes details for setup, installation and configuration of Cloud Pak for Integration for the described use case.
 As we conclude our work on the practicum, we expect you are versed with the basic fundamentals and usage of Cloud Pak for Integration.
 This will assist you in your journey to Modernizing Applications and keep upspeed with the technology and trends.
 

