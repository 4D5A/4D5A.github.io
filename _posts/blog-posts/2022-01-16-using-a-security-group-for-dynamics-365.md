---
layout: post
title: Using a security group for Dynamics 365
gh-repo: 4D5A
gh-badge: [follow]
categories: [blog]
tags: [Microsoft 365, Dynamics 365, Azure Active Directory]
after-content: [disclaimer-notice.html]
---

As organizations move away from on-premises Active Directory (AD) to Azure Active Directory (AAD), there will be resources which were previously managed on-premises Active Directory security groups. When an account is created in AAD the account does not exist in on-premise AD (unlike if you create the account in on-premise AD and use Azure AD Connect to sync the account to AAD). As a result, if you have cloud resources like Dynamics 365 where access requires being assigned the necessary license in Microsoft 365 and also to be a member of a AAD synched security group, you reach a decision point. Do you choose to delete the AAD account, create it in the on-premises AD, and sync the account to AAD? That certainly makes it more difficult to move away from on-premises AD.

The first step is to make a report of the current members of the on-premises AD security group which is synched to AAD.

Then you can create a new AAD security group. It is important to create a new AAD security group and to add the members of the existing on-premises AD security group to it because you can add on-premises AD accounts that are synched to AAD to an AAD security group but you cannot add AAD accounts to an on-premises AD security group that is synched to AAD.

<img src="{{ 'assets/img/2022-01-16-using-a-security-group-for-dynamics-365/you-need-to-be-added-to-a-security-group-to-access-this-environment.png' | relative_url }}" alt="Dynamics 365 notMemberOfOrg" />

Lastly, you need to configure the Dynamics 365 environment to require accounts to be members of your new AAD security group. While Microsoft provides documentation about how to configure a Dynamics 365 environment to require accounts to be members of a security group, I assumed I would do that from Dynamics 365. If you read further in Microsoft’s documentation, you will get to the section, [“Associate a Security Group with a Dataverse Environment.”](https://docs.microsoft.com/en-us/power-platform/admin/control-user-access#associate-a-security-group-with-a-dataverse-environment)

In short, once you create the new AAD security group and add the desired users to it, you need to go to the Power Platform Center. I expected this to be in Dynamics 365. It was not. You can access Power Platform Admin Center by visiting [https://admin.powerplatform.microsoft.com/environments](https://admin.powerplatform.microsoft.com/environments) or by opening Office 365 Admin Center, clicking All admin centers, and clicking Power Apps. In the Power Platform Admin Center, you need to click the name of your Dynamics 365 environment. You need to click the of the environment, not the “…” (more environment actions).

In the Dynamics 365 environment, you will click Edit.

That will open a blade with settings. Near the bottom of the blade is a setting for specifying a security group which accounts must be a member of in order to be authorized to access the environment. You will need to click the edit button, click the name of the security group you want to configure for the Dynamics 365 environment (you can only configure one security group per Dynamics 365 environment), and click Done.