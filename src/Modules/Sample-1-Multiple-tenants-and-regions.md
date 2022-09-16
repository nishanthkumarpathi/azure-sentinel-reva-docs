# Sample1: Multiple tenants and regions

The Contoso Corporation is a multinational business with headquarters in London. Contoso has offices around the world, with important hubs in New York City and Tokyo. Recently, Contoso has migrated their productivity suite to Office 365, with many workloads migrated to Azure.

### Contoso tenants

Due to an acquisition several years ago, Contoso has two Azure AD tenants: `contoso.onmicrosoft.com` and `wingtip.onmicrosoft.com`. Each tenant has its own Office 365 instance and multiple Azure subscriptions, as shown in the following image:

![Diagram of Contoso tenants, each with separate sets of subscriptions.](https://docs.microsoft.com/en-us/azure/sentinel/media/best-practices/contoso-tenants.png)

### Contoso compliance and regional deployment

Contoso currently has Azure resources hosted in three different regions: US East, EU North, and West Japan, and strict requirement to keep all data generated in Europe within Europe regions.

Both of Contoso's Azure AD tenants have resources in all three regions: US East, EU North, and West Japan

### Contoso resource types and collection requirements

Contoso needs to collect events from the following data sources:

* Office 365
* Azure AD Sign-in and Audit logs
* Azure Activity
* Windows Security Events, from both on-premises and Azure VM sources
* Syslog, from both on-premises and Azure VM sources
* CEF, from multiple on-premises networking devices, such as Palo Alto, Cisco ASA, and Cisco Meraki
* Multiple Azure PaaS resources, such as Azure Firewall, AKS, Key Vault, Azure Storage, and Azure SQL
* Cisco Umbrella

Azure VMs are mostly located in the EU North region, with only a few in US East and West Japan. Contoso uses Microsoft Defender for servers on all their Azure VMs.

Contoso expects to ingest around 300 GB/day from all of their data sources.

### Contoso access requirements

Contoso’s Azure environment already has a single existing Log Analytics workspace used by the Operations team to monitor the infrastructure. This workspace is located in Contoso AAD tenant, within EU North region, and is being used to collect logs from Azure VMs in all regions. They currently ingest around 50 GB/day.

The Contoso Operations team needs to have access to all the logs that they currently have in the workspace, which include several data types not needed by the SOC, such as **Perf**, **InsightsMetrics**, **ContainerLog**, and more. The Operations team must _not_ have access to the new logs that will be collected in Microsoft Sentinel.

### Contoso Incident Response requirements

### **Scenario 1 : Credential access**

Multiple passwords reset by user following suspicious sign-in

This scenario makes use of alerts produced by **scheduled analytics rules**.

**MITRE ATT\&CK tactics:** Initial Access, Credential Access

**MITRE ATT\&CK techniques:** Valid Account (T1078), Brute Force (T1110)

**Data connector sources:** Microsoft Sentinel (scheduled analytics rule), Azure Active Directory Identity Protection

**Description:** Fusion incidents of this type indicate that a user reset multiple passwords following a suspicious sign-in to an Azure AD account. This evidence suggests that the account noted in the Fusion incident description has been compromised and was used to perform multiple password resets in order to gain access to multiple systems and resources. Account manipulation (including password reset) may aid adversaries in maintaining access to credentials and certain permission levels within an environment. The permutations of suspicious Azure AD sign-in alerts with multiple passwords reset alerts are:

* **Impossible travel to an atypical location leading to multiple passwords reset**
* **Sign-in event from an unfamiliar location leading to multiple passwords reset**
* **Sign-in event from an infected device leading to multiple passwords reset**
* **Sign-in event from an anonymous IP leading to multiple passwords reset**
* **Sign-in event from user with leaked credentials leading to multiple passwords reset**

### **Scenario 2 :** Compute resource abuse

#### Multiple VM creation activities following suspicious Azure Active Directory sign-in <a href="#multiple-vm-creation-activities-following-suspicious-azure-active-directory-sign-in" id="multiple-vm-creation-activities-following-suspicious-azure-active-directory-sign-in"></a>

**MITRE ATT\&CK tactics:** Initial Access, Impact

**MITRE ATT\&CK techniques:** Valid Account (T1078), Resource Hijacking (T1496)

**Data connector sources:** Microsoft Defender for Cloud Apps, Azure Active Directory Identity Protection

**Description:** Fusion incidents of this type indicate that an anomalous number of VMs were created in a single session following a suspicious sign-in to an Azure AD account. This type of alert indicates, with a high degree of confidence, that the account noted in the Fusion incident description has been compromised and used to create new VMs for unauthorized purposes, such as running crypto mining operations. The permutations of suspicious Azure AD sign-in alerts with the multiple VM creation activities alert are:

* **Impossible travel to an atypical location leading to multiple VM creation activities**
* **Sign-in event from an unfamiliar location leading to multiple VM creation activities**
* **Sign-in event from an infected device leading to multiple VM creation activities**
* **Sign-in event from an anonymous IP address leading to multiple VM creation activities**
* **Sign-in event from user with leaked credentials leading to multiple VM creation activities**

## Deliverables / Tasks

**Team 1:** Capture Customer requriements, propose a solution and Handover the requirements to technical team and create a project plan with activities.&#x20;

**Team 2:** Create a High Level Implementation Design and Structure for Implemenation of Security Operations in Contoso

**Team 3:** Create a Incident Response flow chart for two scenrios given by Contoso

**Team 4**: Customer is looking for Metrics and Dashboards regarding the Day to Day Security Operations, based on scenrio, create KPI and Metric Dashboards.
