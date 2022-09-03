# Setup Environment

## Objectives

This module guides you through the deployment of the Microsoft Sentinel Training Lab solution that will be used in all subsequent modules.

#### Prerequisites

To get started with Microsoft Sentinel, you must have a Microsoft Azure subscription. If you do not have a subscription, you can sign up for a free account [here](https://azure.microsoft.com/en/free).

Permissions to create a resource group in your Azure subscription.

### Exercise 1: The Microsoft Sentinel workspace

In this exercise we will show you how to create a brand new Microsoft Sentinel workspace. If you already have a pre-existing one that you would like to use, you can skip to [Exercise 2](Module-1-Setting-up-the-environment.md#exercise-2-deploy-the-microsoft-sentinel-training-lab-solution).

1. Navigate to the [Azure Portal](http://portal.azure.com) and log in with your account.
2. In the top search bar, type _Microsoft Sentinel_ and click on **Microsoft Sentinel**.
3.

    <figure><img src="../.gitbook/assets/image (2) (2).png" alt=""><figcaption></figcaption></figure>
4. In the Microsoft Sentinel screen, click **Create** at the top left.
5. You can choose to add Microsoft Sentinel to an existing Log Analytics workspace or build a new one. We will create a new one, so click on **Create a new workspace**.

![deployment](../Images/deployment2.png)

6\. In the Create Log Analytics workspace page, fill out the form as follows:

* **Subscription**: choose the Azure subscription where you would like to deploy the Microsoft Sentinel workspace
* **Resource Group**: select an existing resource group or create a new resource group (recommended) that will host the lab resources
* **Region**: from the drop down, select the Azure region where the lab will be located
* **Workspace Name**: provide a name for the Microsoft Sentinel workspace. Please note that the workspace name should include 4-63 letters, digits or '-'. The '-' shouldn't be the first or the last symbol

![deployment](../Images/deployment3.png)

8\. Click **Review + create** and then **Create** after the validation completes. The creation takes a few seconds.

9\. You will be redirected back to the _Add Microsoft Sentinel to a workspace_. Type the name of your new workspace in the search box, select your workspace and click **Add** at the bottom.

![deployment](../Images/deployment4.png)

Your Microsoft Sentinel workspace is now ready to use!

### Exercise 2: Deploy the Microsoft Sentinel Training Lab Solution

In this exercise you will deploy the Training Lab solution into your existing workspace. This will ingest pre-recorded data (\~20 MBs) and create several other artifacts that will be used during the exercises.

1. Go to your SEntinel Overview Section and Under Content Management, Select Content Hub.

<figure><img src="../.gitbook/assets/image (2) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

2\. In the Search Bar, Enter "Training Lab" .

<figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

3\. Make Sure you Click on Install.

<figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

### Exercise 3: Configure Microsoft Sentinel Playbook

In this exercise, we will configure a Playbook that will be later used in the lab. This will allow the playbook to access Sentinel.

1. Navigate to the resource group where the lab has been deployed.
2. In the resource group you should see an API Connection resource called **azuresentinel-Get-GeoFromIpAndTagIncident**, click on it.

![playbook1](../Images/playbook1.png)

1. Click on Edit **API connection** under **General**.

![playbook2](../Images/playbook2.png)

1. Click on **Authorize** and a new window will open to chose an account. Pick the user that you want to authenticate with. This should normally be the same user that you're logged in with.

![playbook3](../Images/playbook3.png)

1. Click **Save**.

![playbook4](../Images/playbook4.png)

You can now continue to [**Module 2 - Data Connectors**](Module-2-Data-Connectors.md)
