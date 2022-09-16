# Sample 2: Single tenant with multiple clouds



Fabrikam is an organization with headquarters in New York City and offices all around the United States. Fabrikam is starting their cloud journey, and still needs to deploy their first Azure landing zone and migrate their first workloads. Fabrikam already has some workloads on AWS, which they intend to monitor using Microsoft Sentinel.

#### Fabrikam tenancy requirements

Fabrikam has a single Azure AD tenant.

#### Fabrikam compliance and regional deployment

Fabrikam has no compliance requirements. Fabrikam has resources in several Azure regions located in the US, but bandwidth costs across regions is not a major concern.

#### Fabrikam resource types and collection requirements

Fabrikam needs to collect events from the following data sources:

* Azure AD Sign-in and Audit logs
* Azure Activity
* Security Events, from both on-premises and Azure VM sources
* Windows Events, from both on-premises and Azure VM sources
* Performance data, from both on-premises and Azure VM sources
* AWS CloudTrail
* AKS audit and performance logs

#### Fabrikam access requirements

The Fabrikam Operations team needs to access:

* Security events and Windows events, from both on-premises and Azure VM sources
* Performance data, from both on-premises and Azure VM sources
* AKS performance (Container Insights) and audit logs
* All Azure Activity data

The Fabrikam SOC team needs to access:

* Azure AD Signin and Audit logs
* All Azure Activity data
* Security events, from both on-premises and Azure VM sources
* AWS CloudTrail logs
* AKS audit logs
* The full Microsoft Sentinel portal

## Deliverables / Tasks

**Team 1:** Capture Customer requriements, propose a solution and Handover the requirements to technical team and create a project plan with activities.&#x20;

**Team 2:** Create a High Level Implementation Design and Structure for Implemenation of Security Operations in Fabrikam&#x20;

**Team 3:** Create a Incident Response flow chart for two scenrios given by Fabrikam&#x20;

**Team 4**: Customer is looking for Metrics and Dashboards regarding the Day to Day Security Operations, based on scenrio, create KPI and Metric Dashboards.
