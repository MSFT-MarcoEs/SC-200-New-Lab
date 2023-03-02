---
lab:
    title: 'Microsoft Sentinel Onboarding'
    module: 'Learning Path 5 - Configure your Microsoft Sentinel environment'
---

# Learning Path 5: Configure your Microsoft Sentinel environment

## Lab scenario

You are a Security Operations Analyst working at a company that is implementing Microsoft Sentinel. 

Microsoft Sentinel is a scalable, cloud-native solution that provides:
- Security information and event management (SIEM)
- Security orchestration, automation, and response (SOAR)

Microsoft Sentinel delivers intelligent security analytics and threat intelligence across the enterprise. 

With Microsoft Sentinel, you get a single solution for attack detection, threat visibility, proactive hunting, and threat response.

## Lab objectives

In this lab, you will complete the following exercises:

- Exercise 1: Prepare the environment
- Exercise 2: Onboard Microsoft Sentinel 

> For all the resources in this lab, we are using the **East US** region. Verify with your instructor this is the region to use for class. 

# Instructions

## Exercise 1: Prepare the environment

In this exercise, you will complete the following tasks: 

- Task 1: Deploy an Azure virtual machine


### Task 1: Deploy an Azure virtual machine

In this task, you will create an Azure Virtual Machine to get recommendations from Microsoft Defender for Endpoint.

1. Sign-in to the Azure portal **`https://portal.azure.com/`**.

    >**Note**: Sign in to the Azure portal using an account that has the Owner or Contributor role in the Azure subscription you are using for this lab.

1. Open the Cloud Shell by clicking the first icon in the top right of the Azure Portal. When prompted, select PowerShell and Create storage. Wait until the Storage Account is created.

1. Ensure **PowerShell** is selected in the drop-down menu in the upper-left corner of the Cloud Shell pane.

1. In the PowerShell session within the Cloud Shell pane, run the following to create a resource group that will be used in this lab:
  
    ```PowerShell
    New-AzResourceGroup -Name SC200LP05 -Location 'EastUS'
    ```

1. In the PowerShell session within the Cloud Shell pane, run the following to create a new Azure virtual machine. 

    ```PowerShell
    New-AzVm -ResourceGroupName "SC200LP05" -Name "myVM" -Location 'EastUS' -VirtualNetworkName "myVnet" -SubnetName "mySubnet" -SecurityGroupName "myNetworkSecurityGroup" -PublicIpAddressName "myPublicIpAddress" -PublicIpSku Standard -OpenPorts 80,3389 -Size Standard_DS1_v2 
    ```
    
1.  When prompted for credentials:

    |Setting|Value|
    |---|---|
    |User |**localadmin**|
    |Password|**Use the same password as the admin account provided**|

    >**Note**: Wait for the deployment to complete, it should take less than 5 minutes.

1. In the PowerShell session within the Cloud Shell pane, run the following to confirm that the virtual machine named **myVM** was created and its **ProvisioningState** is **Succeeded**.

    ```PowerShell
    Get-AzVM -Name 'myVM' -ResourceGroupName 'SC200LP05' | Format-Table
    ```

1. Close the Cloud Shell pane.


## Exercise 2: Onboard Microsoft Sentinel

In this exercise, you will complete the following tasks:

- Task 1: Enable Microsoft Sentinel
- Task 2: Set up data connectors


### Task 1: Enable Microsoft Sentinel

In this task, you will on-board and configure Microsoft Sentinel.

1. In the Azure portal, in the **Search resources, services, and docs** text box at the top of the Azure portal page, type **Microsoft Sentinel** and press the **Enter** key.

1. Click the **Create** button.

1. Click the **Create a new workspace** button. 

1. On the **Basics** tab of the **Create Log Analytics workspace** blade, specify the following settings (leave others with their default values):

    |Setting|Value|
    |---|---|
    |Subscription|the name of the Azure subscription you are using in this lab|
    |Resource group|**SC200LP05**|
    |Name|MySentinelWorkspace|
    |Region|**East US**|

1. Click the **Review + create** button.

1. On the **Review + create** tab of the **Create Log Analytics workspace** blade, click **Create**.

1. When the *Deployment Succeded* notification appears, click the **Refresh** button to show your newly created *Log Analytics workspace*.

    >**Note:** The default workspaces created by Microsoft Defender for Cloud are not shown in the list. You can't install Microsoft Sentinel on these workspaces.

1. Select your workspace and click **Add** to onboard Microsoft Sentinel.

    >**Important:** Once deployed on a workspace, Microsoft Sentinel does not currently support the moving of that workspace to other resource group or subscription.

1. Read the message under *Microsoft Sentinel free trial activated* and click **OK** to continue


### Task 2: Set up data connectors

In this task, you will configure Microsoft Sentinel to use the Azure Activity and Windows Security Events via AMA data connectors.

1. In the Azure portal, on the **Microsoft Sentinel - Overview** blade, under the **Configuration** section, click **Data connectors**. 

1. On the **Data connectors** blade, review the list of available connectors and that none are in a Connected state. 

1. Type **Windows** into the search bar and select the entry representing the **Windows Security Events via AMA** data connector (hide the menu bar on the left using **<<** if needed), review its description and status, and then click **Open connector page**.

1. On the **Windows Security Events via AMA** data connector blade, the **Instructions** tab should be selected, note the **Prerequisites** and scroll down to the **Configuration**.

1. The Azure Monitor Agent (AMA) relies on Data Collection Rules (DCR) to send data to a Log Analytics workspace. Click **Create data collection rule**.

1. Type **CommonSecurityEvents** for *Rule Name* and click the **Next: Resources** button.

1. Click the **Add resource(s)** button and type **MyVM** in the search box.

1. Select the checkbox of *MyVM* and click the **Apply** button.

1. Click the **Next: Collect** button to continue.

1. Hover the mouse pointer over the **(i)** to learn about the different stream options. Select **Common** and click the **Next: Review + create** button.

1. Read the summary of your configurations and click the **Create** button. You should observe three succeeded status messages: **Creating Data Collection Rule Association succeeded, Successfully enabled identity, and Successfully installed extension**.

    >**Note**: You can check the *Notifications* bell icon to verify the three successful tasks.

1. On the **Windows Security Events via AMA** data connector blade, click the **Next steps** tab.

1. Review the available *Recommended workbooks*, *Query samples*, and *Relevant analytics templates* available for this connector.

1. Go back to the **Microsoft Sentinel - Data Connectors** page by closing the data connector blade when you click the **x** at the top right.

1. Type **Azure** into the search bar and select the entry representing the **Azure Activity** data connector (hide the menu bar on the left using **<<** if needed), review its description and status, and then click **Open connector page**.

1. On the **Azure Activity** data connector blade, the **Instructions** tab should be selected, note the **Prerequisites** and scroll down to the **Configuration**. Take note of the information describing the connector update. Your Azure Pass subscription never used the legacy connection method so you can skip step 1 (the *Disconnect All* button will be grayed out) and proceed to step 2.

1. In step 2 **Connect your subscriptions through diagnostic settings new pipeline**, review the *Launch the Azure Policy Assignment wizard and follow the steps* instructions then click **Launch the Azure Policy Assignment wizard**.

1. On the **Configure Azure Activity logs to stream to specified Log Analytics workspace** (Assign Policy page) **Basics** tab, under *Scope* click the ellipsis **(...)** button.

1. In the **Scope** blade choose your Azure Pass subscription from the drop-down subscription list and click the **Select** button at the bottom of the page.

    >**Note**: *Do not* choose a Resource Group

1. Click the **Next** button **twice** at the bottom of the **Basics** tab to proceed to the **Parameters** tab. 

1. On the **Parameters** tab, under *Primary Log Analytics workspace*, click the ellipsis **(...)** button.

1. In the **Primary Log Analytics workspace** page, make sure your Azure pass subscription is selected and use the **workspaces** drop-down to select the Log Analytics workspace you are using for Sentinel. When done click the **Select** button at the bottom of the page.

1. Click the **Next** button at the bottom of the **Parameters** tab to proceed to the **Remediation** tab. 

1. On the **Remediation** tab select the **Create a remediation task** checkbox. This will enable the *Configure Azure Activity logs to stream to specified Log Analytics workspace* in the **Policy to remediate** drop-down. In the **System assigned identity location** drop-down, select the region (East US for example) you selected earlier for your Log Analytics workspace.

1. Click the **Review + Create** button at the bottom of the  **Remediation** tab.

1. Click the **Create** button. You should observe three succeeded status messages: **Creating policy assignment succeeded, Role Assignments creation succeeded, and Remediation task creation succeeded**.

    >**Note**: You can check the *Notifications* bell icon to verify the three successful tasks.

1. On the **Azure Activity** data connector blade, click the **Next steps** tab.

1. Review the available *Recommended workbooks*, *Query samples*, and *Relevant analytics templates* available for this connector.

1. Go back to the **Microsoft Sentinel - Data Connectors** page by closing the data connector blade when you click the **x** at the top right.

1. Type **Network** into the search bar and select the entry representing the **Network Security Groups (NSG)** data connector (hide the menu bar on the left using **<<** if needed), review its description and status, and then click **Open connector page**.

1. Repeat the same steps you have done for the Azure Activity connector to onbard NSG data into Microsoft Sentinel using Azure Policy.

1. Type **Azure** into the search bar and select the entry representing the **Azure Storage Account** data connector (hide the menu bar on the left using **<<** if needed), review its description and status, and then click **Open connector page**.

1. On the **Azure Activity** data connector blade, the **Instructions** tab should be selected, note the **Prerequisites** and scroll down to the **Configuration**.

1. Note the steps described under *Inside you Storage Account resources:* and click **Open Storage Accounts**.

1. Select the name of Storage Account created for your Azure Cloud Shell.

1. Scroll down the left blade and under *Monitoring*, click **Diagnostic Settings**. **Hint:** Do not select the (classic) option.

1. Select **blob** under your Storage Account name and click **Add diagnostic setting**.

1. Type **BlobToSentinelLAW** for *Diagnostic settings name*.

1. Select all checkboxes under *Logs* and *Metrics*, and next to *Send to Log Analytics Workspace*.

1. Make sure you workspace is selected and click **Save**.

    >**Note:** This *Diagnostic Settings* configuration is the same that we configured prevously by using Azure Policy, but in this case we set it up manually.

1. In the Azure portal, in the **Search resources, services, and docs** text box at the top of the Azure portal page, type **Microsoft Sentinel** and press the **Enter** key.

1. Select your Microsoft Sentinel workspace, scroll down the left blade and under the **Configuration** section, click **Data connectors**. 

1. You should now see at least 1 data connector in a connected state.

1. Search for the **Azure Activity** data connector and after selecting, review the **Data received** graph. **Hint:** You might have to click the **Refresh** button or the entire Edge browser page to see the updated data.

    >**Note**: It may take over 15 minutes before the Status shows "Connected" and the graph displays Data received.

1. Search for the other data connectors in the *Connected* state and review the *Data received* graph. **Hint:** You can filter the connectors by **Status: Connected**.

# End of lab