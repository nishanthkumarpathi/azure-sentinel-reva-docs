# Sample1: Multiple tenants and regions

The Contoso Corporation is a multinational business with headquarters in London. Contoso has offices around the world, with important hubs in New York City and Tokyo. Recently, Contoso has migrated their productivity suite to Office 365, with many workloads migrated to Azure.

#### Contoso tenants

Due to an acquisition several years ago, Contoso has two Azure AD tenants: `contoso.onmicrosoft.com` and `wingtip.onmicrosoft.com`. Each tenant has its own Office 365 instance and multiple Azure subscriptions, as shown in the following image:

![Diagram of Contoso tenants, each with separate sets of subscriptions.](https://docs.microsoft.com/en-us/azure/sentinel/media/best-practices/contoso-tenants.png)

#### Contoso compliance and regional deployment

Contoso currently has Azure resources hosted in three different regions: US East, EU North, and West Japan, and strict requirement to keep all data generated in Europe within Europe regions.

Both of Contoso's Azure AD tenants have resources in all three regions: US East, EU North, and West Japan

#### Contoso resource types and collection requirements

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

#### Contoso access requirements

Contoso’s Azure environment already has a single existing Log Analytics workspace used by the Operations team to monitor the infrastructure. This workspace is located in Contoso AAD tenant, within EU North region, and is being used to collect logs from Azure VMs in all regions. They currently ingest around 50 GB/day.

The Contoso Operations team needs to have access to all the logs that they currently have in the workspace, which include several data types not needed by the SOC, such as **Perf**, **InsightsMetrics**, **ContainerLog**, and more. The Operations team must _not_ have access to the new logs that will be collected in Microsoft Sentinel.
