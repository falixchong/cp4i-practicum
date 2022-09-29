IBM CP4I Practicum Lab Book for Scenario 3:
=================
This Practicum Lab Book explains and focuses on the solution preparation and implementation for Practicum Scenario 3 using Cloud Pak for Integration (CP4I). The book will cover the solution architecture and the details of how to best implement the solution for the given scenario. This will help accelerate adoption of Cloud Pak for Integration.

Table of contents
=================

<!--ts-->
   * [Topic 1: Introduction and Scenario Details](#topic-1-introduction-and-scenario-details)
   * [Topic 2: Introduction to Clusters and Openshift](#topic-3-introduction-to-clusters-and-openshift)
   * [Topic 3: Solution Architecture](#topic-2-solution-architecture)
   * [Topic 4: Running Transformation Advisor](#topic-4-running-transformation-advisor)
      * [Installing IBM App Connect Enterprise (ACE) & Running Transformation Advisor (TADataCollector)](#installing-ibm-app-connect-enterprise-ace--running-transformation-advisor-tadatacollector)
   * [Topic 5: Environment Setup and Configuration](#topic-4-environment-setup-and-configuration)
      * [Provisioning Cluster](#provisioning-cluster)
      * [Creating Projects](#creating-projects)
      * [Installing Cloud Pak for Integration on Openshift](#installing-cloud-pak-for-integration-on-openshift)
      * [Creating Platform Navigator](#creating-platform-navigator)
      * [Creating Integration Dashboard](#creating-integration-dashboard)
      * [Creating MQ Queue Manager](#creating-mq-queue-manager)
   * [Topic 6: Refactor, Build and Deployment](#topic-6--refactor-build-and-deployment)
      * [Importing Asset into IBM ACE Toolkit and creating local integration server](#importing-asset-into-ibm-ace-toolkit-and-creating-local-integration-server)
      * [Refactor ACE REST to REST Message flow](#refactor-ace-rest-to-rest-message-flow)
      * [Refactor ACE MQ Message flow using MQ Client Connection. Introduce new RestToMQApp message flow to expose putting MQ messages](#refactor-ace-mq-message-flow-using-mq-client-connection-introduce-new-resttomqapp-message-flow-to-expose-putting-mq-messages)
      * [Build ACE message flow into BAR file](#build-ace-message-flow-into-bar-file)
      * [Deploy BAR file to CP4I Integration Servers](#deploy-bar-file-to-cp4i-integration-servers)
   * [Topic 7: Conclusion](#conclusion)
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

[Back to Top](Table of contents)

&nbsp;
&nbsp;
&nbsp;
&nbsp;
&nbsp;
&nbsp;


# Topic 2: Introduction to Clusters and Openshift

## Clusters and Openshift

### Introduction to Cluster

If you're completely new to the concept of clusters, do follow the quick and easy tutorial [here](https://cloud.ibm.com/docs/openshift?topic=openshift-openshift_tutorial) to learn more
> With Red Hat® OpenShift® on IBM Cloud®, you can create highly available clusters with virtual or bare metal worker nodes that come installed with the Red Hat OpenShift on IBM Cloud Container Platform orchestration software. You get all the advantages of a managed offering for your cluster infrastructure environment, while using the Red Hat OpenShift tooling and catalog that runs on Red Hat Enterprise Linux for your app deployments.


### Introduction to Openshift

OpenShift is a platform that allows you to run containerized applications and workloads and is powered by Kubernetes. It is an offering that comes with Red Hat support, regardless of where you choose to run your applications and workloads. 

One of the big advantages of OpenShift is being able to take advantage of public and private resources which includes bare metal or virtualized hardware whether it is on-premise or on a cloud provider. 

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/openshift-overview.png?raw=true)

This is the high level OpenShift Container Platform overview.

For developers, OpenShift has two different ways of enabling them to work with their platform. They can take advantage of either the CLI or a web console. 

[Back to Top](#topic-1-introduction-and-scenario-details)

&nbsp;
&nbsp;
&nbsp;
&nbsp;
&nbsp;
&nbsp;


# Topic 2: Solution Architecture
Below describe the high-level solution architecture for a simple scenario 3 solution.
By using IBM IIB/ACE transformation advisor's TADataCollector, we can analyse the current monolith IIB/ACE application. A report will be generated to recommend what are needed to refactor, in order to achieve a microservice architecture build.

The refactor code can later be deployed in a microservices architecture on Redhat Openshift with CP4I (ACE + MQ) environment 

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/scenario3-architecture.png?raw=true)


[Back to Top](#topic-1-introduction-and-scenario-details)


&nbsp;
&nbsp;
&nbsp;
&nbsp;
&nbsp;
&nbsp;



# Topic 3: Running Transformation Advisor

## Installing IBM App Connect Enterprise (ACE) & Running Transformation Advisor (TADataCollector)

1.	Install IBM App Connect Enterprise for developers (also called ACE). Make sure to select the correct download package for your OS (Windows, Linux, Mac).
Click on "Download" button on the following link:  https://www.ibm.com/docs/en/app-connect/12.0?topic=enterprise-download-ace-developer-edition-get-started 
The version used in this practicum is `12.0.3`

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/AppConnect1.png?raw=true)

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/AppConnect2.png?raw=true)

   Complete the installation through the installer package you just downloaded for your OS.  eg: `12.0.3.0-ACE-MAC64-DEVELOPER-UNSIGNED`. You can use the IBM ACE Installation page as a guide to complete the installation.


2. Once installed, open the installed ACE toolkit. A view similar to the screenshot below will launch.

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/AppConnect3.png?raw=true)

3. Open the Integration Console (IBM App connect Enterprise Toolkit -> Open Integration console)

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/AppConnect4.png?raw=true)

4. IBM ACE console will launch on your screen as below:

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/AppConnect5.png?raw=true)

5. Run the Transformation Advisor by running `TADataCollector` on this console and passing Scenario3 Assets-IIB IIBV10_Broker_backup.zip file through command line, as shown below: 

```
TADataCollector ace run /<path_to_assets_on_local_machine>/IIBV10_Broker_backup.zip
```

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/AppConnect6.png?raw=true)

6. Once the `TADataCollector` command runs successfully, it will generate recommendation files under `/tmp/TADataCollector/output/IIB1` folder. Open the following directory to access the generated files

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/AppConnect7.png?raw=true)

7.	Open the recommendations.html file located in `/tmp/TADataCollector/output/IIB1` folder & Read the recommendations made by IBM Transformation Advisor

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/AppConnect8.png?raw=true)

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/AppConnect9.png?raw=true)


[Back to Top](#topic-1-introduction-and-scenario-details)

&nbsp;
&nbsp;
&nbsp;
&nbsp;
&nbsp;
# Topic 4: Environment Setup and Configuration

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

[Back to Top](#topic-1-introduction-and-scenario-details)

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


[Back to Top](#topic-1-introduction-and-scenario-details)

&nbsp;
## Creating Platform Navigator

1. Login to Red Hat Open Shift Cluster and navigate to Operators --> Installed Operators and search for “Platform Navigator”

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/PlatformNavigator1.png?raw=true)
  
2. Click and navigate to “Platform Navigator” tab and click “Create PlatformNavigator

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/PlatformNavigator2.png?raw=true)
  
3. Click Create PlatformNavigator. The Create Platform Navigator panel opens, and offers two methods for configuring the resource; the Form viewand the YAML view. The Form view is selected by default:

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/PlatformNavigator3.png?raw=true)


[Back to Top](#topic-1-introduction-and-scenario-details)

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


[Back to Top](#topic-1-introduction-and-scenario-details)


&nbsp;
## Creating Integration Dashboard

1. Go to IBM Cloud Pak home and click on the hamburger on top left --> click Integration Instances 

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/PlatformNavigator.png?raw=true)
 
2. Click “Create Instance” on top left of the page

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/CreateInstance-AppConnect.png?raw=true)
 
3. Select Integration Dashboard and click next

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/IntegrationDashboard.png?raw=true)
 
4. Select either Production or Quick Start --> click next

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/IntegrationDashboardType.png?raw=true)
 
5. Key-in the details of the environment and proceed to create the dashboard.

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/IntegrationDashboard-Env.png?raw=true)
 
6. Upon successful creation of Integration Dashboard below message will appear on the browser. 

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/IntegrationDashboard-Final.png?raw=true)


[Back to Top](#topic-1-introduction-and-scenario-details)

&nbsp;
## Creating MQ Queue Manager

1. Go to IBM Cloud Pak Home, click on “Messaging” as highlighted in the screen below

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/Messging.png?raw=true)
 
2. This will redirect to a Messaging screen as below. Click on Create an instance to create a queue manager.

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/MessgnScreen.png?raw=true)
 
3. Select “Quick start” option from this screen, and click on Next.

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/CreateQMngr.png?raw=true)
 
4. Modify the details for your queue manager as below:
   
   a. License acceptance – Toggle the button from OFF to ON state


   ![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/Licence.png?raw=true)
 
 
 
   b. Select “Type of availability” from dropdown as SingleInstance  
 
   
  ![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/AvailabilityType.png?raw=true)
 
 
   c. Select “Type of Volume” from the drop down as persistent-claim
 
  ![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/VolumeType.png?raw=true)
 
 
 5. Lastly click on “Create” from the top right corner and queue will be created. 
   You will be redirected to a new page, showing the details of your newly created Queue manager.
   
 ![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/QMngrReady.png?raw=true)
 

6. Click on queue manager name --> It will open up MQ Console

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/MQConsole.png?raw=true)
 
 
7. Click on  manage --> quickstart to open queue manager

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/ChannlQuickStrt.png?raw=true)
 

8. Navigate to Communication --> App Channel --> Click Create 

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/CreateAppChnl.png?raw=true)
 
  
 Your MQ environment is **ready!!!**
 
 [Back to Top](#topic-1-introduction-and-scenario-details)
 
&nbsp;
&nbsp;
&nbsp;
&nbsp;
&nbsp;
&nbsp; 
# Topic 5 : Refactor, Build and Deployment

&nbsp; 
## Importing Asset into IBM ACE Toolkit and creating local integration server
1. Import a project in ACE using navigation --> File -> Import -> IBM Project Interchange

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/HTTP1.png?raw=true)

2. Browse on your local machine to add the MessageFlow_PI.zip file from the Scenario 3 assets as shown below

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/HTTP2.png?raw=true)

3. Open the assets into ACE as shown in screenshot below - 

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/HTTP3.png?raw=true)

4. Click on finish to import the Project. Your assets are successfully imported.

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/HTTP4.png?raw=true)

   This will open child window to enter details of the server. Once completed, hit finish.
![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/HTTP5.png?raw=true)



[Back to Top](#topic-1-introduction-and-scenario-details)   

&nbsp; 
## Refactor ACE REST to REST Message flow

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/scenario3-application-architecture-ace-rest-rest.png?raw=true)


1. [Build HTTPResponseApp ace message flow into BAR file](#build-ace-message-flow-into-bar-file)

2. [Deploy bar file of BAR for HttpResponseApp flow into CP4I Integration Server](#deploy-bar-file-to-cp4i-integration-servers)  


3. Open ACE and navigate to HttpRequestApp —> Expand to open folder Flows —> double-click RequestService.msgflow

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/refactor-http-1.png?raw=true)

4. Proceed to make the following changes to each component as below:

   a. Select HttpInput on Flow Exerciser. Properties associated with it will show up at the bottom. 
	    Change the path suffix url  to /requestService
      
![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/ChangeProperties1.png?raw=true)
  
  
   b. Select HTTPRequest and replace the URL as per the cluster URL.
   
   Default WebService URL : http://localhost:7800/responseService

   Changed URL : http://**is-01-toolkit-request-app-3-http-cp4i.cp4intpg-wdc04-ttwakv-8946bbc006b7c6eb0829d088919818bb-0000.us-east.containers.appdomain.cloud**/responseService

   
   Before proceeding with the above change, keep the URL handy **(highlighted in bold)** by copying it from - 
   
   OCP Console --> Administrator View --> Networking --> Select Routes --> Search for the Response Integration server created. 
   Take a note of the URL in the “Location” column.
   
![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/refactor-http-2.png?raw=true)

   Save the flow in ACE after replacing the http://localhost:7800
  
![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/refactor-http-3.png?raw=true)
  
5. Create a BAR file for this flow and deploy it to a new Integration Server in OCP.

6. [Build HTTPRequestApp ace message flow into BAR file](#build-ace-message-flow-into-bar-file)

7. [Deploy bar file of BAR for HttpRequestApp flow into CP4I Integration Server](#deploy-bar-file-to-cp4i-integration-servers)  

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/refactor-http-4.png?raw=true)

8. Test the deployed BARs

   a. Open Terminal on your local machine

   b. Use curl command to test your deployment 


```   
curl -v http://is-01-toolkit-request-app-3-http-cp4i.cp4intpg-wdc04-ttwakv-8946bbc006b7c6eb0829d088919818bb-0000.us-east.containers.appdomain.cloud/responseService
```
```
curl -v http://is-01-toolkit-request-app-3-http-cp4i.cp4intpg-wdc04-ttwakv-8946bbc006b7c6eb0829d088919818bb-0000.us-east.containers.appdomain.cloud/requestService
```

Please replace 
**http://is-01-toolkit-request-app-3-http-cp4i.cp4intpg-wdc04-ttwakv-8946bbc006b7c6eb0829d088919818bb-0000.us-east.containers.appdomain.cloud** part with the URL of your environment


9. Receiving 200 OK on the terminal validates successful deployment

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/refactor-http-5.png?raw=true)

[Back to Top](#topic-1-introduction-and-scenario-details)

&nbsp; 
## Refactor ACE MQ Message flow using MQ Client Connection. Introduce new RestToMQApp message flow to expose putting MQ messages

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/scenario3-application-architecture-ace-mq.jpg?raw=true)

1. To obtain MQ Endpoint, login to your cluster and navigate to Networking --> Services --> Search "MQ" --> Copy Hostname and Port as highlighted

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/HostName.png?raw=true)
 
 
2. Navigate to ACE and open MQ Flow
 
![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/ACE-MQFlow.png?raw=true)
 

3. On diagram at the right --> Select MQInput --> It will display properties section below.
    Navigate to MQ Connection --> It should display the properties of MQ Connection
    
    
![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/ACEMQ2.png?raw=true)
 
 
4. Select the Connection Type as "MQ Client Connection Properties" --> Specify "Destination Queue Manager Name" , "Queue Manager Host Name" 
   (Retrieved in step - 15), "Listener Port" (Retrieved in step - 15) and "Channel Name". Hit save.


![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/ACEMQ3.png?raw=true)
 

5. Repeat the same configuration for MQOutput

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/ACEMQoutput.png?raw=true)



[Back to Top](#topic-1-introduction-and-scenario-details)


## Running mq_ace_lab.mqsc

There are different layers of authorization and authentication configured on the Channel access. To simplify the exercise, we will proceed to disable to Channel security authentication and authorization. Below steps will assist to disable - 

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
oc project cp4i(your project name)
```


![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/MQAC7.png?raw=true)

5. Run command as below to get the pod name of pod running MQ. Copy the pod name to be used later.

```
oc get pods|grep mq 

```

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/MQAC8.png?raw=true)

6. Change Directory to the location of your mqsc file. Use the following command to upload mqsc file to the MQ pod

```
oc exec -it quickstart-cp4i-queue-ibm-mq-0(this is your pod’s name) runmqsc QUICKSTART < mq_ace_lab.mqsc
```

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/MQAC9.png?raw=true)

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/MQAC10.png?raw=true)


MQ is ready and running on your environment!!!


[Back to Top](#topic-1-introduction-and-scenario-details)


&nbsp;
## Build ACE message flow into BAR file

1. To generate BAR file for the assets. Select BAR --> New --> BAR File 
   Add a name (**HttpResponseApp** or **HttpRequestApp** or **MQ_CLIENT_APP**) for the BAR file and click    Finish.

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/HTTP8.png?raw=true)

2. Select resources to include in the BAR file (**HttpResponseApp** or **HttpRequestApp** or **MQ_CLIENT_APP**), then click on “Build and Save” to generate BAR file. 

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/HTTP9.png?raw=true)


3. Once Build, the BAR file will appear under BARs on the left side of Application Development panel. 

4. Repeat above **step 1** to **step 3** to create BAR file for **HttpRequestApp** and **MQ_CLIENT_APP**


[Back to Top](#topic-1-introduction-and-scenario-details)

&nbsp;
## Deploy BAR file to CP4I Integration Servers
 
 1. Navigate to IBM Cloud Pak for Integrations home page. Select Run --> Integrations --> It will redirect you to IBM APP Connect

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/IBMAppConnect.png?raw=true)
 
 2. Create new Integration Server to deploy BAR file for MQ --> Select QuickStart tool kit integration --> Hit Next

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/DeployBAR.png?raw=true)
 
 3. Provide the BAR file to be deployed to the server and hit next.
 
 **Bar file can be directly dragged and dropped onto CP4I browser console**

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/CreateIntServer.png?raw=true)
 
 4. Pass the configuration screen by clicking "Next" proceeding to "Common Settings". Validate the settings as shown in the snapshot below.

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/CreateIntServer2.png?raw=true)
 
 5. Hit "Create" and wait until the status of the server has changed to "Ready"

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/IntServerStatus.png?raw=true)
 
 6. The status changes to "Ready"

![alt text](https://github.com/falixchong/cp4i-practicum/blob/master/images/IntServerReadyStatus.png?raw=true)

    

[Back to Top](#topic-1-introduction-and-scenario-details)     
    
    
# Topic 7: Conclusion  
 
 The above completes details for setup, installation and configuration of Cloud Pak for Integration for the described use case.
 As we conclude our work on the practicum, we expect you are versed with the basic fundamentals and usage of Cloud Pak for Integration.
 This will assist you in your journey to Modernizing Applications and keep upspeed with the technology and trends.
 

