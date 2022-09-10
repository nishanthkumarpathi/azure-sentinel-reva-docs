# Workspace Design Decision

{% embed url="https://docs.microsoft.com/en-us/azure/sentinel/media/best-practices/workspace-decision-tree.png" %}



The following sections provide a full-text version of this decision tree, including the following notes referenced from the image:

[Note #1](https://docs.microsoft.com/en-us/azure/sentinel/design-your-workspace-architecture#note1) | [Note #2](https://docs.microsoft.com/en-us/azure/sentinel/design-your-workspace-architecture#note2) | [Note #3](https://docs.microsoft.com/en-us/azure/sentinel/design-your-workspace-architecture#note3) | [Note #4](https://docs.microsoft.com/en-us/azure/sentinel/design-your-workspace-architecture#note4) | [Note #5](https://docs.microsoft.com/en-us/azure/sentinel/design-your-workspace-architecture#note5) | [Note #6](https://docs.microsoft.com/en-us/azure/sentinel/design-your-workspace-architecture#note6) | [Note #7](https://docs.microsoft.com/en-us/azure/sentinel/design-your-workspace-architecture#note7) | [Note #8](https://docs.microsoft.com/en-us/azure/sentinel/design-your-workspace-architecture#note8) | [Note #9](https://docs.microsoft.com/en-us/azure/sentinel/design-your-workspace-architecture#note9) | [Note #10](https://docs.microsoft.com/en-us/azure/sentinel/design-your-workspace-architecture#note10)

### Step 1: New or existing workspace?

Do you have an existing workspace that you can use for Microsoft Sentinel?

* **If not, and you'll be creating a new workspace** in any case, continue directly with [step 2](https://docs.microsoft.com/en-us/azure/sentinel/design-your-workspace-architecture#step-2-keeping-data-in-different-azure-geographies).
* **If you have an existing workspace** that you might use, consider how much data you'll be ingesting.
  * **If you'll be ingesting **_**more**_** than 100 GB / day**, we recommend that you use a separate workspace for the sake of cost efficiency.
  * **If you'll be ingesting **_**less**_** than 100 GB / day**, continue with [step 2](https://docs.microsoft.com/en-us/azure/sentinel/design-your-workspace-architecture#step-2-keeping-data-in-different-azure-geographies) for further evaluation. Consider this question again when it arises in [step 5](https://docs.microsoft.com/en-us/azure/sentinel/design-your-workspace-architecture#step-5-collecting-any-non-soc-data).

### Step 2: Keeping data in different Azure geographies?

* **If you have regulatory requirements to keep data in different Azure geographies**, use a separate Microsoft Sentinel workspace for each Azure region that has compliance requirements. For more information, see [Region considerations](https://docs.microsoft.com/en-us/azure/sentinel/best-practices-workspace-architecture#region-considerations).
* **If you don't need to keep data in different Azure geographies**, continue with [step 3](https://docs.microsoft.com/en-us/azure/sentinel/design-your-workspace-architecture#step-3-do-you-have-multiple-azure-tenants).

### Step 3: Do you have multiple Azure tenants?

* **If you have only a single tenant**, continue directly with [step 4](https://docs.microsoft.com/en-us/azure/sentinel/design-your-workspace-architecture#step-4-splitting-billing--charge-back).
* **If you have multiple Azure tenants**, consider whether you're collecting logs that are specific to a tenant boundary, such as Office 365 or Microsoft 365 Defender.
  * **If you don't have any tenant-specific logs**, continue directly with [step 4](https://docs.microsoft.com/en-us/azure/sentinel/design-your-workspace-architecture#step-4-splitting-billing--charge-back).
  *   **If you **_**are**_** collecting tenant-specific logs**, use a separate Microsoft Sentinel workspace for each Azure AD tenant. Continue with [step 4](https://docs.microsoft.com/en-us/azure/sentinel/design-your-workspace-architecture#step-4-splitting-billing--charge-back) for other considerations.

      [Decision tree note #1](https://docs.microsoft.com/en-us/azure/sentinel/design-your-workspace-architecture#decision-tree): Logs specific to tenant boundaries, such as from Office 365 and Microsoft Defender for Cloud, can only be stored in the workspace within the same tenant.

      Although it is _possible_ to use custom collectors to collect tenant-specific logs from a workspace in another tenant, we do not recommend this due to the following disadvantages:

      * Data collected by custom connectors will be ingested into custom tables. Therefore, you won’t be able to use all the built-in rules and workbooks.
      * Custom tables are not considered by some of the built-in features, such as UEBA and machine learning rules.
      * Additional cost and effort required for the custom connectors, such as using Azure Functions and Logic Apps.

      If these disadvantages are not a concern for your organization, continue with [step 4](https://docs.microsoft.com/en-us/azure/sentinel/design-your-workspace-architecture#step-4-splitting-billing--charge-back) instead of using separate Microsoft Sentinel workspaces.

### Step 4: Splitting billing / charge-back?

If you need to split your billing or charge-back, consider whether the usage reporting or manual cross-charge works for you.

* **If you **_**do not**_** need to split your billing or charge-back**, continue with [step 5](https://docs.microsoft.com/en-us/azure/sentinel/design-your-workspace-architecture#step-5-collecting-any-non-soc-data).
*   **If you **_**do**_** need to split your billing or charge-back**, consider whether [usage reporting or manual cross-charge](https://docs.microsoft.com/en-us/azure/sentinel/billing) will work for you.

    * **If usage reporting or manual cross-charging works for you**, continue with [step 5](https://docs.microsoft.com/en-us/azure/sentinel/design-your-workspace-architecture#step-5-collecting-any-non-soc-data).
    * **If **_**neither**_** usage reporting or manual cross-charging will work for you**, use a separate Microsoft Sentinel workspace for each cost owner.

    [Decision tree note #2](https://docs.microsoft.com/en-us/azure/sentinel/design-your-workspace-architecture#decision-tree): For more information, see [Microsoft Sentinel costs and billing](https://docs.microsoft.com/en-us/azure/sentinel/billing).

### Step 5: Collecting any non-SOC data?

* **If you are not collecting any non-SOC data**, such as operational data, you can skip directly to [step 6](https://docs.microsoft.com/en-us/azure/sentinel/design-your-workspace-architecture#step-6-multiple-regions).
*   **If you **_**are**_** collecting non-SOC data**, consider whether there are any overlaps, where the same data source is required for both SOC and non-SOC data.

    **If you **_**do**_** have overlaps between SOC and non-SOC data**, treat the overlapping data as SOC data only. Then, consider whether the ingestion for _both_ SOC and non-SOC data individually is less than 100 GB / day, but more than 100 GB / day when combined:

    * **Yes**: Proceed with [step 6](https://docs.microsoft.com/en-us/azure/sentinel/design-your-workspace-architecture#step-6-multiple-regions) for further evaluation.
    * **No**: We do not recommend using the same workspace for the sake of cost efficiency. Proceed with [step 6](https://docs.microsoft.com/en-us/azure/sentinel/design-your-workspace-architecture#step-6-multiple-regions) for further evaluation.

    In either case, for more information, see [note 10](https://docs.microsoft.com/en-us/azure/sentinel/design-your-workspace-architecture#note10).

    **If you have **_**no**_** overlapping data**, consider whether the ingestion for _both_ SOC and non-SOC data individually is less than 100 GB / day, but more than 100 GB / day when combined:

    * **Yes**: Proceed with [step 6](https://docs.microsoft.com/en-us/azure/sentinel/design-your-workspace-architecture#step-6-multiple-regions) for further evaluation. For more information, see [note 3](https://docs.microsoft.com/en-us/azure/sentinel/design-your-workspace-architecture#combining-your-soc-and-non-soc-data).
    * **No**: We do not recommend using the same workspace for the sake of cost efficiency. Proceed with [step 6](https://docs.microsoft.com/en-us/azure/sentinel/design-your-workspace-architecture#step-6-multiple-regions) for further evaluation.

### **Combining your SOC and non-SOC data**

[Decision tree note #3](https://docs.microsoft.com/en-us/azure/sentinel/design-your-workspace-architecture#decision-tree): While we generally recommend that customers keep a separate workspace for their non-SOC data so that non-SOC data is not subject to Microsoft Sentinel costs, there may be situations where combining SOC and non-SOC data is less expensive than separating them.

For example, consider an organization that has security logs ingesting at 50 GB/day, operations logs ingesting at 50 GB/day, and a workspace in the East US region.

The following table compares workspace options with and without separate workspaces.

&#x20;Note

Costs and terms listed in the following table are fake, and used for illustrative purposes only. For up-to-date cost information, see the Microsoft Sentinel pricing calculator.

| Workspace architecture                                                                                                                                     | Description                                                                                                                                                                                                                                                                                                                                           |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| <p>The SOC team has its own workspace, with Microsoft Sentinel enabled.<br><br>The Ops team has its own workspace, without Microsoft Sentinel enabled.</p> | <p><strong>SOC team</strong>:<br>Microsoft Sentinel cost for 50 GB/day is $6,500 per month.<br>First three months of retention are free.<br><br><strong>Ops team</strong>:<br>- Cost of Log Analytics at 50 GB/day is around $3,500 per month.<br>- First 31 days of retention are free.<br><br>The total cost for both equals $10,000 per month.</p> |
| Both SOC and Ops teams share the same workspace with Microsoft Sentinel enabled.                                                                           | <p>By combining both logs, ingestion will be 100 GB / day, qualifying for eligibility for Commitment Tier (50% for Sentinel and 15% for LA).<br><br>Cost of Microsoft Sentinel for 100 GB / day equals $9,000 per month.</p>                                                                                                                          |

In this example, you'd have a cost savings of $1,000 per month by combining both workspaces, and the Ops team will also enjoy 3 months of free retention instead of only 31 days.

This example is relevant only when both SOC and non-SOC data each have an ingestion size of >=50 GB/day and <100 GB/day.

[Decision tree note #10](https://docs.microsoft.com/en-us/azure/sentinel/design-your-workspace-architecture#decision-tree): We recommend using a separate workspace for non-SOC data so that non-SOC data isn't subjected to Microsoft Sentinel costs.

However, this recommendation for separate workspaces for non-SOC data comes from a purely cost-based perspective, and there are other key design factors to examine when determining whether to use a single or multiple workspaces. To avoid double ingestion costs, consider collecting overlapped data on a single workspace only with table-level Azure RBAC.

### Step 6: Multiple regions?

* **If you are collecting logs from Azure VMs in a **_**single**_** region only**, continue directly with [step 7](https://docs.microsoft.com/en-us/azure/sentinel/design-your-workspace-architecture#step-7-segregating-data-or-defining-boundaries-by-ownership).
*   **If you are collecting logs from Azure VMs in **_**multiple**_** regions**, how concerned are you about the data egress cost?

    [Decision tree note #4](https://docs.microsoft.com/en-us/azure/sentinel/design-your-workspace-architecture#decision-tree): Data egress refers to the [bandwidth cost](https://azure.microsoft.com/pricing/details/bandwidth/) for moving data out of Azure datacenters. For more information, see [Region considerations](https://docs.microsoft.com/en-us/azure/sentinel/best-practices-workspace-architecture#region-considerations).

    * If reducing the amount of effort required to maintain separate workspaces is a priority, continue with [step 7](https://docs.microsoft.com/en-us/azure/sentinel/design-your-workspace-architecture#step-7-segregating-data-or-defining-boundaries-by-ownership).
    *   If the data egress cost is enough of a concern to make maintaining separate workspaces worthwhile, use a separate Microsoft Sentinel workspace for each region where you need reduce the data egress cost.

        [Decision tree note #5](https://docs.microsoft.com/en-us/azure/sentinel/design-your-workspace-architecture#decision-tree): We recommend that you have as few workspaces as possible. Use the [Azure pricing calculator](https://azure.microsoft.com/pricing/calculator/?service=azure-sentinel) to estimate the cost and determine which regions you actually need, and combine workspaces for regions with low egress costs. Bandwidth costs may be only a small part of your Azure bill when compared with separate Microsoft Sentinel and Log Analytics ingestion costs.

        For example, your cost might be estimated as follows:

        * 1,000 VMs, each generating 1 GB / day;
        * Sending data from a US region to an EU region;
        * Using a 2:1 compression rate in the agent

        The calculation for this estimated cost would be: `1000 VMs * (1GB/day ÷ 2) * 30 days/month * $0.05/GB = $750/month bandwidth cost`

        This sample cost would be much less expensive when compared with the monthly costs of a separate Microsoft Sentinel and Log Analytics workspace.

        &#x20;Note

        Listed costs are fake and are used for illustrative purposes only. For up-to-date cost information, see the Microsoft Sentinel pricing calculator.

### Step 7: Segregating data or defining boundaries by ownership?

* **If you **_**do not**_** need to segregate data or define any ownership boundaries**, continue directly with [step 8](https://docs.microsoft.com/en-us/azure/sentinel/design-your-workspace-architecture#step-8-controlling-data-access-by-data-source--table).
*   **If you **_**do**_** need to segregate data or define boundaries based on ownership**, does each data owner need to use the Microsoft Sentinel portal?

    *   If each data owner must have access to the Microsoft Sentinel portal, use a separate Microsoft Sentinel workspace for each owner.

        [Decision tree note #6](https://docs.microsoft.com/en-us/azure/sentinel/design-your-workspace-architecture#decision-tree): Access to the Microsoft Sentinel portal requires that each user have a role of at least a [Microsoft Sentinel Reader](https://docs.microsoft.com/en-us/azure/role-based-access-control/built-in-roles), with **Reader** permissions on all tables in the workspace. If a user does not have access to all tables in the workspace, they'll need to use Log Analytics to access the logs in search queries.
    * If access to the logs via Log Analytics is sufficient for any owners without access to the Microsoft Sentinel portal, continue with [step 8](https://docs.microsoft.com/en-us/azure/sentinel/design-your-workspace-architecture#step-8-controlling-data-access-by-data-source--table).

    For more information, see [Permissions in Microsoft Sentinel](https://docs.microsoft.com/en-us/azure/sentinel/roles).

### Step 8: Controlling data access by data source / table?

* **If you **_**do not**_** need to control data access by source or table**, use a single Microsoft Sentinel workspace.
*   **If you **_**do**_** need to control data access by source or table**, consider using [resource-context RBAC](https://docs.microsoft.com/en-us/azure/sentinel/resource-context-rbac) in the following situations:

    * If you need to control access at the row level, such as providing multiple owners on each data source or table
    * If you have multiple, custom data sources/tables, where each one needs separate permissions

    In other cases, when you do _not_ need to control access at the row level, provide multiple, custom data sources/tables with separate permissions, use a single Microsoft Sentinel workspace, with table-level RBAC for data access control.

**Considerations for resource-context or table-level RBAC**

When planning to use resource-context or table level RBAC, consider the following information:

* [Decision tree note #7](https://docs.microsoft.com/en-us/azure/sentinel/design-your-workspace-architecture#decision-tree): To configure resource-context RBAC for non-Azure resources, you may want to associate a Resource ID to the data when sending to Microsoft Sentinel, so that the permission can be scoped using resource-context RBAC. For more information, see [Explicitly configure resource-context RBAC](https://docs.microsoft.com/en-us/azure/sentinel/resource-context-rbac#explicitly-configure-resource-context-rbac) and [Access modes by deployment](https://docs.microsoft.com/en-us/azure/azure-monitor/logs/workspace-design).
* [Decision tree note #8](https://docs.microsoft.com/en-us/azure/sentinel/design-your-workspace-architecture#decision-tree): [Resource permissions](https://docs.microsoft.com/en-us/azure/azure-monitor/logs/manage-access) or [resource-context](https://docs.microsoft.com/en-us/azure/azure-monitor/logs/workspace-design) allows users to view logs only for resources that they have access to. The workspace access mode must be set to **User resource or workspace permissions**. Only tables relevant to the resources where the user has permissions will be included in search results from the **Logs** page in Microsoft Sentinel.
* [Decision tree note #9](https://docs.microsoft.com/en-us/azure/sentinel/design-your-workspace-architecture#decision-tree): [Table-level RBAC](https://docs.microsoft.com/en-us/azure/azure-monitor/logs/manage-access) allows you to define more granular control to data in a Log Analytics workspace in addition to the other permissions. This control allows you to define specific data types that are accessible only to a specific set of users.&#x20;
