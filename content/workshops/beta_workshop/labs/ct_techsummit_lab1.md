---
weight: 4
title: "A day as a Control Tower Administrator"
---

<link rel="stylesheet" type="text/css" href="ct.css">
![AWS](images/AWS_logo_RGB-small.png)

## Overview ##

AWS Control Tower provides the easiest way to set up and govern a new, secure, multi-account AWS environment based on best practices established through AWS’ experience working with thousands of enterprises as they move to the cloud. With AWS Control Tower, builders can provision new AWS accounts in a few clicks, while you have peace of mind knowing your accounts conform to your company-wide policies. 

AWS Service Catalog enables organizations to create and manage catalogs of approved IT services for use on AWS. In a multi-account environment, the AWS Service Catalog portfolios can be managed centrally at the Management account and distribute across the remaining accounts across the AWS Organizations. 

In this lab, we will walkthrough: 1)some of the common tasks an AWS Control Tower Administrator perform on a day-to-day basis; 2) a way to centrally manage the commonly deployed self-service catalogs. This lab is broken in to two tasks:

* Task-1: Common tasks of AWS Control Tower.
* Task-2: Centrally manage the commonly deployed services.

##### Terminologies used in this lab:
* **Management Account**: AWS Control Tower Management account, where we deployed the Control Tower service.
* **Spoke Account**: An Account in AWS Control Tower Environment, other than the Management account.
* **Administrator**: AWS SSO User with AdministratorAccess permissions.
* **EndUser**: AWS SSO User with minimum required permissions.
* **Portfolios**: AWS Service Catalog portfolio is a collection of products, together with configuration information.
* **Product**: AWS Service Catalog product is an IT service that you want to make available for deployment on AWS.
* **Constraints**: Constraints control the ways that specific AWS resources can be deployed for a product.

##### Things to know before Getting Started
* The instructions below are based on old console of CloudFormation and they could vary if you are using new console of CloudFormation.
* _HIGHLY RECOMMEND_ to switch your CloudFormation console to **_Previous Console_**. This can be done by clicking on Hamburger Icon top left, and  **_Previous Console_** on the CloudFormation Console.
<img src="images/Previous_console.png" width="10%" height="10%">
* If you prefer to use the latest console, please feel free to do so with appropriate changes at the required steps of this lab. Instructions not included in this lab.
* For Task-1, please stick to one of the **supported regions** (Oregon, Ohio, N.Virginia, Ireland).


----


## Task-1 : Common tasks of AWS Control Tower ##

John is an application architect in a financial services organization. His organization has compliance requirements to store the last three-year log files of several applications running in his environment. 

Sam is the cloud architect, who is part of the CCOE team in the same organization and responsible for cloud architecture. Sam decided to use the pre-configured centralized logging account in the AWS Control Tower to archive log files from all the applications running in multiple accounts. Sam wants to ensure that the environment is secure and at the same time, give flexibility to John's team to design his applications without a need to reach out to the Cloud Architecture team.

Sam provided access to John to log in to log archive account and desgin S3 buckets as required for his applications.

In this part of the lab, we will walk through:

* Enable a guardrail on the Organizational Unit.
* Creating an AWS SSO user in AWS Control Tower.
* Capturing any violations in the Control Tower Environment as the application teams deploy the resources. 

#### 1.1 Log in to AWS Control Tower 

PS: Please make sure you have the below information before getting started with this lab. If you don't have the below details **STOP HERE** and reach out to one of the Lab Admin.

- [ ] Sign-in URL
- [ ] Email
- [ ] Password

<details><summary>1.1.1 Log in to AWS Control Tower Environment</summary>

In this section you are going access the AWS Control Tower service from AWS Console.

1. Copy and paste the **Sign-in URL** you recieved in to your favorite browser. 

1. Under **Username**, enter the **Email** you collected earlier.

1. Enter the **Password**, that you collected above.

1. You will be prompted to reset the password.

1. Enter the same password you collected above under **Current Password**. 

1. Pick your own **New Password**, Repeat it and click on **Update Password**. 

1. You will be redirected to AWS SSO login page.

1. Click on **AWS Account(3)** to expand.

1. Click on **`Account-no`(Management)** to expand.

1. Click on **Management Console** Next to **AWSAdministratorAccess** to go to AWS Console.
![SSOLogin](images/AWS_SSO_Login_Screen.png)
1. Type _`Tower`_ under **Find Services** and select _Control Tower_.
![FindCT](images/Find_Control_Tower_Service.png)
1. You will be redirected to Control Tower **Dashboard**.
![CTDashboard](images/Control_Tower_Dashboard.png)

</details>

#### 1.2 Enable a guardrail on Organizational Unit 

In this section you are going to enable a guardrail on an organization unit to watch out for any violations to the company policies. In this case, guardrail will check for any S3 buckets with no versioning enabled.

<details><summary>1.2.1 Enable a Strongly recommended Guardrail</summary>

1. Right click and open the [Control Tower console](https://us-west-2.console.aws.amazon.com/controltower/home/landing?region=us-west-2#)

1. On the Control Tower **Dashboard**, select **Guardrails** from the left side bar. 

1. Click on the little arrow **">"** on the top right corner to go next page of Guardrails 

1. Click on **Disallow S3 buckets that are not versioning enabled**
![CTGuardrails](images/Guardrails_enable.png)
1. Scroll down to **Organizational units enabled** and Click on **Enable guardrail on OU**
![CTGuardrails](images/enable_guardrail_2.png)
1. Select the **Core** OU and click on **Enable guardrail on OU**. Wait for the green banner on the top of the screen.
![CTGuardrails](images/enable_guardrails_3.png)
1. **Please note :** Enabling guardrail will instantiate CloudFormation Stackset update in the background and could take 2-5 minutes for the operation to get effective.

</details>

Congratulations, you sucessfully enabled a guardrail on the Security OU [old installations: Core OU] to watch for any violations to the company policy of versioning their S3 buckets.

#### 1.3 Create an AWS SSO User, Group and Permission set

In this section, you will create a new AWS SSO user and grant permissions for the new user to access the Log Archive account.

<details><summary>1.3.1 Create new permission set</summary>

1. On the left sidebar, select **Users and access**.

1. Under **Federated access management** click on **View in AWS Single Sign-On**.
![AWSSSO_1](images/AWS_SSO_Image_1.png)

1. Select **AWS accounts** on the left side bar.

1. In **AWS Accounts** page, select **Permission sets** tab, and click **Create permission set** button.
![AWSSSO_2](images/AWS_SSO_Image_2.png)

1. In **Create new permission** set page, select **Create a custom permission set**.

1. Type in _`LogArchiveAdminAccess`_ for **Name** and enter some **Description** to the Role.

1. Under **What policies do you want to include in your permission set?**, select **Attach AWS managed policies**.
![AWSSSO_3](images/AWS_SSO_Image_3.png)
1. Under **Attach AWS Managed polices** search bar, type in and add the following
	```
	AWSCloudFormationFullAccess AmazonS3FullAccess IAMReadOnlyAccess AmazonSNSReadOnlyAccess
	```
1. Select the all policies and click on **Create** button.
![AWSSSO_4](images/AWS_SSO_Image_4.png)
</details>

<details><summary>1.3.2 Create an AWS SSO user / group </summary>

1. Select **Directory** from the left sidebar

1. Under **Users**, click on **Add user** button.
![Create_SSO_User_1](images/Create_SSO_User_1.png)

1. Fill in the Email address (could be _`[your-alias]`_+logAdmin@amazon.com), confirm email address

1. Select **Generate a one-time password....** and type in all other required fields.

1. Click on **Next:Groups** button. 
![Create_SSO_User_2](images/Create_SSO_User_2.png)

1. Click on **Create group** on top of the panel. 

1. Type in _`LogArchiveAdminGroup`_ under **Group name**, with some appropriate description.

1. Click on **Create** button and you will be redirected to **Add users to groups** page.
![Create_SSO_User_3](images/Create_SSO_User_3.png)

1. On the search bar, type in _`LogArchiveAdminGroup`_ and select the newly created group.

1. Click on **Add user** button.
![Create_SSO_User_4](images/Create_SSO_User_4.png)

1. Copy and paste **User portal URL:** value in a notepad. (PS: Avoid using Copy details link on to right, as it messes up few characters)  

1. Click on **Show password** next to _One-time password:_, note the value in a notepad.

1. Click on **Close** button to go back to the **Directory** page
![Create_SSO_User_5](images/Create_SSO_User_5.png)
</details>

<details><summary>1.3.3 Assign permission set to AWS SSO User/Group </summary>

1. Now click on the **AWS accounts** on the left sidebar and select check box next to **Log archive**.

1. Click on **Assign users**.
![Assign_Permission_Set_1](images/Assign_Permission_Set_1.png)
1. Under **Assign Users**, select the **Groups** tab,

1. Type in _`LogArchiveAdminGroup`_ in the search bar and select the **LogArchiveAdminGroup** Group
![Assign_Permission_Set_2](images/Assign_Permission_Set_2.png)

1. Click **Next: Permission sets**

1. In **Select permission sets** page, select checkbox next to **LogArchiveAdminAccess** 

1. Click on **Finish**
![Assign_Permission_Set_3](images/Assign_Permission_Set_3.png)
1. Click on **Proceed to AWS Accounts**
</details>

<details><summary>1.3.4 Logout from AWS SSO as Administrator</summary>
1. On AWS SSO Console, **Logout** as Administrator by clicking on **Sign Out** link on the top right corner. 
![AWS_Admin_Logout](images/AWS_Admin_LogOut.png)
</details>

You successfully created an AWS SSO User, Group and a Permission set. You also Assigned the permission set to the AWS SSO Group. We will use these credentials in both Task-1 and Task-2.

#### 1.4 Create S3 Bucket on Log Archive Account

In this section you will log in to Log Archive Account using username and password you set up for a newly created AWS SSO User in step 1.3.2. You will also create a private S3 bucket using CloudFormation. 

<details><summary> 1.4.1 Log in to Log Archive Account as New User</summary>

1. Log in to the **Sign-in URL**, provide the Email address and one-time password you saved in 1.3.2 as the Username and Password.

1. You will be prompted to reset the password. Please follow the instructions on screen to reset the password.

1. Once you set up the password sucessfully, you will be redirected to AWS SSO login page.

1. Click on **AWS Account(1)** to expand

1. Click on **`Account-no`(Log Archive)** to expand.

1. Click on **Management Console** Next to **LogArchiveAdminAccess** to go to AWS Console.
![AWSSSO_User_1](images/SSO_Login_LogArchive_1.png)
</details>

<details><summary>1.4.2 Launch an S3 bucket using CloudFormation stack</summary>

1. Right click and Open in new tab [CloudFormation Console-Launch Stack](https://console.aws.amazon.com/cloudformation#/stacks/new?stackName=AppX-LogSetup&templateURL=https://aws-service-catalog-reference-architectures-ct.s3.amazonaws.com/s3/sc-s3-private-ra-url.yaml)

1. Click on **Next** button

1. Click on **Next** button

1. Scroll down and click on **Next**

1. Review the content and click **Create**

1. Wait for the Stack status to become **CREATE_COMPLETE**
![CFT_User_1](images/CFT_User_Launch_1.png)
</details>

On completion of this stack, a private S3 bucket will be deployed on your LogArchive Account.

#### 1.5 Check for the violations on AWS Control Tower 

In this section let us see how the violations are reported and take a remediation action.

<details><summary>1.5.1 Log in to AWS Control Tower Dashboard as Administrator</summary>

1. Log out as a **non-admin** user from the AWS SSO Console by clicking on **Sign out** at the top right corner. 

1. Follow the steps mentioned on 1.1.1 to login in back to Management account as **AWS Control Tower Administrator**. 

1. Find the Control Tower service and go to the **Dashboard**. You could use link https://console.aws.amazon.com/controltower/home/dashboard to jump to dashboard directly.
</details>

<details><summary>1.5.2 Check for the Noncompliant resources on dashboard </summary>

1. Scroll down and check under **Noncompliant resources**. The newly created S3 Bucket will be listed as a Noncompliant resource.

1. You could scroll down further and see the Compliance status of the **Organizational units** and **Accounts**. 
![NonCompliance_1](images/NonCompliance_1.png)
1. It could **take 2-3 minutes** for the Noncompliance to be reported here. Refresh the screen if you don't see the Noncompliant resource.
</details>

<details><summary> 1.5.3 Corrective Action </summary>
The possible corrective actions here are to notify the Business user(John) to fix the permissions on S3 bucket or Administrator take the corrective action.
For this lab-session we will DELETE the resource and move on to next task.
**PS:** **Important** Please do not skip this step. It is important to delete this stack.

1. Choose to open the [Control Towwer Console](https://console.aws.amazon.com/controltower/home/accounts)

1. Click on **Log archive** and Copy the **Account ID**.

1. Expand Username next to bell icon on the top right corner.

1. Select **Switch Role** option, and **Switch Role** again (if you get additional screen)
![Switch_Role](images/Switch_Role.png)

1. Under **Account**, Paste the Log archive AccountId that you collected above

1. Type in **AWSControlTowerExecution** for Role, and click on **Switch Role**
![Switch_Account_Log](images/Switch_Account_Log.png)

1. You will be switched to **Log archive** account.

1. On Log archive account, go to CloudFormation console. You may use link https://console.aws.amazon.com/cloudformation/ 

1. Select the check box next to the stack named **AppX-LogSetup**, and select **Outputs** tab

1. Click on the URL shown under value of **BucketAccessURL** to go to S3 bucket properties page
![Bucket_Properties](images/Bucket_Properties.png)
1. Click on **Versioning**, select **Enable versioning**, and **Save**
![Versioning_Enable](images/Versioning_Enable.png)
1. Make sure Versioning as show as Enabled 
![Versioning_Check](images/Versioning_Check.png)

1. Expand Username next to bell icon on the top right corner.

1. Select **Back to AWSReservedSSO_AWSAdministratorAccess_......** to switch back to Management Account.
![Switch_Back_Admin](images/Switch_Back_To_Admin.png)

1. Check back the [AWS Control Tower Dashboard](https://us-west-2.console.aws.amazon.com/controltower/home/dashboard?region=us-west-2), and the Noncompliances reported should be cleared. If not cleared wait for **2-5 minutes**.
</details>

**[Optional]**: For sanity purposes, please delete the stack to delete the resources created for this lab.

Congratulations! you completed the Task-1 successfully, please proceed to the next Task.

----

## Task-2 : Centrally manage the commonly deployed services

Sam’s team identified a common set of workloads that application team uses. Both teams collaboratively created CloudFormation templates for these patterns with all best practices, and fine tuning needed for applications, along with organizations compliance and tagging policies in place. 

Sam’s team want to make these portfolios available to all the newly vended AWS accounts as they get vended. We will see how Sam used Service Catalog to centrally manage these resources from Management and distribute to rest of the accounts as they get vended.

**PS:** Due to logistical issues of this lab, we will use Security OU [old installations: Core OU] which consists of log and audit AWS accounts to share the Service catalog portfolios to vended AWS accounts. In practice, these portfolios from Management are shared to various other OUs in the organization.

#### 2.1 Grant AWS Service Catalog EndUser Access to AWS SSO Account

In this section, we will grant **AWSServiceCatalogEndUserAccess** to the AWS SSO User (_`[your-alias]`_+logAdmin@amazon.com) we created earlier.

<details><summary>2.1.1 Grant Service Catalog EndUser access to an AWS SSO Group</summary>

1. If you logged out as Administrator, please follow the steps mentioned on 1.1.1 to login in back.

1. Access AWS Control Tower **Users and access** using https://console.aws.amazon.com/controltower/home/usersandaccess 

1. Under **Federated access management**, click on **View in AWS Single Sign-On**

1. You will be redirected to AWS SSO Console.

1. Select **AWS accounts** from the left side bar.

1. Click on **Log archive** account, you will be redirected to **Log archive** page.

1. Find **LogArchiveAdminGroup** and click on **Change permission sets** next to it.

1. In **Select permission sets** page, check box **AWSServiceCatalogEndUserAccess** and click on **Save changes**.
</details>

In this step, you granted AWS SSO Group access to Log Archive account with permissions _AWSServiceCatalogEndUserAccess_.

#### 2.2 Configure the Management Account and share a portfolio

In this section you are going to create an AWS Service Catalog Portfolio with a set of Products. Share it with remaining accounts with in an AWS Organizational unit (Security/Core OU in this case).  

<details><summary>2.2.1 Capture the AWS Organizational unit ID</summary>

Follow below steps to capture the AWS Organization ID. You are going to use this value in next section.

1. Copy and Paste https://console.aws.amazon.com/controltower/home/organizationunits on your browser and click on **Core**.

1. Note down the Organizational Unit ID that looks like `ou-xxxx-yyyyzzzz`
![OrganizationUnitId](images/Capture_org_ou_id.png)
</details>

<details><summary>2.2.2 Launch CloudFormation stack to create SC Portfolio on Management account and Share with an OU</summary>

You are going to launch the cloudFormation stack to create the SC Portfolio and share it with an Organizational unit using Service Catalog Organizational sharing.
 
1. Click on [![CreateStack](images/cloudformation-launch-stack.png)](https://console.aws.amazon.com/cloudformation#/stacks/new?stackName=MasterPortfolioToShare&templateURL=https://s3.amazonaws.com/marketplace-sa-resources/configure_ct_portfolio.yaml) 

1. Click on **Next**

1. Replace the default **OrganizationalUnitToShare** value with the OU value you noted in step 2.1.1

1. Click on **Next**

1. Click on **Next**

1. Select checkbox **I acknowledge that AWS CloudFormation might create IAM resources.** and click on **Create**

1. Wait for the Stack Status to change to **CREATE_COMPLETE**

1. Click on the **Outputs** tab and note down both **MasterPortfolioId** and **OrganizationalUnitId**
![OutputsTab](images/Outputs_of_stack.png)
</details>

#### 2.3 Configure the other AWS Accounts in the Organizational Unit

<details><summary>2.3.1 Launch CloudFormation stackset to configure local portfolios on Spoke Accounts</summary>

1. Go to CloudFormation Stacksets page, type in https://console.aws.amazon.com/cloudformation/stacksets/ in the browser 

1. Click on **Create StackSet** button 

1. Select **Specify an Amazon S3 template URL** 

1. Under **Specify Amazon S3 location**, copy and paste <code>https://s3.amazonaws.com/marketplace-sa-resources/ct_spoke_setup_sc_with_roles.yaml</code>

1. Click **Next** 

1. For **StackSet name** type in _`SpokePortfolioShared`_

1. **Please note** that the steps below could vary if you are using latest console of Cloudformation.

1. For **MasterPortfolio**, get the value from output of previous stack you ran in step 2.2.2 and click **Next**

1. Select **Deploy stacks in AWS organizational units. Enter an AWS organizational unit ID** 

1. Type in the _`OrganizationalUnitId`_ value you noted down in step 2.2.2 

1. Under **Specify regions, Available regions**, select the region you are working on (**NOTE:** Should be the same region where you deployed MasterPortofolioToShare)

1. Click on **Add** to move it to **Deployment order** box
![SSDeploymentOptions](images/Set_deployment_options.png)
1. Leave remaining settings to defaults and click on **Next**

1. Under **IAM Admin Role ARN**, expand the options and select **AWSControlTowerStackSetRole**

1. Under **IAM Execution Role Name**, Type in **AWSControlTowerExecution** and click on **Next**
![StackSetRoles](images/StackSet_roles.png)
1. Select checkbox **I acknowledge that AWS CloudFormation might create IAM resources.** and click on **Create**
![IAMCapabilities](images/IAM_Capabilities.png)
1. Wait for all Stacks status change from **OUTDATED** to **CURRENT** 
![StackSetStatus](images/StackSet_Status.png)
</details>

#### 2.4 Access the allowed Catalog products with EndUser permissions

<details><summary>2.4.1 Log in as a Service Catalog EndUser</summary>

1. Logout from the AWS SSO Console as Administrator by clicking on **Sign out** at the top right corner. 

1. Log in to the **Sign-in URL**, provide the Email address and one-time password you saved in 1.3.2 as the Username and Password 

1. You will be redirected to AWS SSO login page

1. Click on **AWS Account(1)** to expand

1. Click on **<Account-no>(Log Archive Account)** to expand.

1. Click on **Management Console** Next to **AWSServiceCatalogEndUserAccess** to go to AWS Console.
![LoginToSSOAsEndUser](images/LoginToSSO_EndUser.png)
</details>

<details><summary>2.4.2 Access the available Products</summary>

1. Access Service Catalog Console using https://console.aws.amazon.com/servicecatalog 

1. You will see list of available Products ready to use 
![EndUserView](images/EndUser_SCProduct.png)

Congratulations, you successfully completed this lab. Due to time-constraints we will not be launching any of the resources. However these products are ready for consumption by the End Users.
</details>

#### 2.5 Cleanup instructions 

Please follow below steps to cleanup the resources that you just deployed.

<details><summary>2.5.1 Delete the stackset used to configure the local portfolios on Spoke accounts </summary>

1. As AWS Control Tower Administrator in Management account, access https://console.aws.amazon.com/cloudformation/stacksets/ to jump to StackSets console. 
1. Check box the _`SpokePortfolioShared`_ and expand **Actions** button. 
1. Select **Manage stacks in Stackset**
1. Select **Delete Stacks** and click on **Next**
1. Select **Delete stacks from AWS organizational units. Enter an AWS organizational unit ID.**
1. Enter the Organizational unit Id that you captured in step 2.2.1. 
1. Scroll down to Specify regions, and select the listed region under **Available regions** and click **Add->** 
1. Click **Next** to continue, and click on **Delete stacks**
1. Once all the Stacks are deleted. Click on **Delete StackSet** button on the top right.
</details>

<details><summary>2.5.2 Delete the stack that created the Master portfolio</summary>

1. Switch back to CloudFormation Console using https://console.aws.amazon.com/cloudformation/  
1. Check box **MasterPortofolioToShare** StackName and click on **Actions**, and **Delete Stack**
1. Click on **Yes,Delete** button. 
</details>

----

## REFERENCES

* [AWS Control Tower](https://aws.amazon.com/controltower/)
* [AWS CloudFormation](https://aws.amazon.com/cloudformation/)
* [AWS Organizations](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_getting-started_concepts.html)
* [AWS Service Catalog](https://aws.amazon.com/servicecatalog/)
* [AWS Single Sign On](https://docs.aws.amazon.com/singlesignon/latest/userguide/what-is.html)
* [AWS Control Tower and AWS Security Hub – Powerful Enterprise Twins](https://aws.amazon.com/blogs/enterprise-strategy/aws-control-tower-and-aws-security-hub-powerful-enterprise-twins/)

----

_Copyright 2019, Amazon Web Services, All Rights Reserved._
