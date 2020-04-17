# Manage Azure Subscriptions and Resources
## Manage Azure Subscriptions
### Configuring Azure Subscription Policies at the Azure Subscription Level
- Azure Policy is a service for *creating, assigning, and managing policies.*
  - These policies enforce different rules over your resources to ensure resource meet corporate standards and SLAs.
  - Azure Policy does this by running evaluations of your resources and scanning for those not compliant
- Implementing Azure Policy
  - A policy definition express what to evaluate and actions
  - Initiative definition is a set of policy definitions to help track your compliance state for a larger goal
  - You can limit the scope to management groups, subscriptions, or resource groups
  - Once the initiative definition is assigned, you can evaluate the state of compliance for all of your resources

## Analyze Resource Utilization and Consumption
### Creating Action Groups
- Enable you to configure a list of actions to take when the alert is triggered
- Ensures the same actions are taken each time an alert is triggered
- Action types
  - Select Email/SMS/Push/Voice
    - Provides the ability to send email, SMS, push notifications, or a voice call
  - Log App, Webhook, IT Service Management, Function
    - Run a Logic App
    - Deploy a Webhook
    - Integrate with an IT management service
    - Run a function app
  - Automation Runbook
    - Run an Azure Automation runbook 

### Configuring Diagnostic Settings on Resources
- Azure Monitor
  - Enables *core monitoring* for Azure services by collecting metrics, activity logs, and diagnostic logs
  - Key capabilities of Azure Monitor include:
    - Monitor and visualize metrics
      - Numerical values available from Azure resources that help you understand health and performance 
    - Query and analyze logs
      - Activity logs, diagnostic logs, and telemetry are monitoring solutions which can provide useful information
    - Setup alerts and actions
      - Notify you of critical conditions and can take automated corrective actions
      - Triggers for alerts can be based on metrics or logs
- Azure Monitor Diagnostic Logs
  - Logs provided by the Azure service that give useful data about the operation of Azure resources and services
  - Logs are constantly updating in real time to provide an accurate assessment of what is going on in the infrastructure
  - Two types of diagnostic logs
    - Tenant logs contain activity that occurs at the tenant level but is outside of the Azure subscription
    - Resource logs contain information from Azure services which deploy resources in Azure
    - ![alt text](images/diagnosticlogs.png "Azure Monitor Diagnostic Logs")
    
### Creating and Resting Alerts
- Azure Monitor alerts can be configured to *notify you* when your resources are performing at a predetermined level or if an event has occurred
  - Benefits of Azure Monitor Alerts
    - Better notification
      - All new alerts use action groups
    - Unified experience
      - Alert metrics and logs are in one place
    - View alerts in Portal
    - Separation of Fired Alerts and Rules
      - Alert rules and fired alerts are differentiated, keeping operational and configuration views separate
- Create Alert rules
  - Define the alert condition including the following elements 
    - Target selection(ie storage account)
    - Alert criteria (ie used capacity)
    - Alert logic (An action is triggered once the disk space capacity has grown past a size)
  - Define the alert details
    - Alert rules name
    - Description
    - Severity (range of 0 to 4)
  - Defining the action group
    - Notify you or your team
    - Take automated actions using webhooks and runbooks

### Analyzing Alerts/Metrics Across Subscriptions
- Azure Metrics
  - Provide details about your Azure Resource at a specific time or over a specified range
  - Information is collected on a regular basis and has built-in alert options
  - Example use cases
    - Analyze
      - Metrics Explorer is used to gather information from different resources
    - Visualize
      - Chart generation and workbooks which combines data
    - Alert
      - Notify people for specific events/triggers
    - Automate
      - Automatic scaling based on metric values
    - Retrieve
      - Can be retrieved using PowerShell, Rest API, and CLI 
    - Export
      - Data can be sent to logs for analysis, Azure Event Hubs, or routed to an external system
    - Archive
      - Metrics can be kept for 93 days
      - Diagnostic logs can be routed to Log Analytics and configured to have a minimum retention of 30 days
      - Activity log entries are stored for 90 days
  - Improvements
    - Improved latency
      - New metric alerts can run as frequently as a minute. 
    - Support for multi-dimensional metrics
      - You can set an alert on dimensional metrics to monitor an interesting segment of the metric
    - More control over metric conditions
    - Combined monitoring of multiple metrics
      - Combine multiple metrics with a single rule
    - Metrics from logs
      - You can extract some data going into Log Analytics and convert it to Azure Metrics 
  - Signals are emitted by the target resource and can Azure Metrics, Activity Log, and Application Insights

### Creating a Baseline for Resources
- Once a baseline has been determined, you can use Metric Alerts with Dynamic thresholds to monitor your resource performance
  - Metric Alerts with Dynamic Thresholds use machine learning to analyze historic data in order to give suggestions regarding possible service issues.
  - Reasons to use dynamic type
    - Scalable alerting
      - Useful when handling multiple resources across multiple subscriptions
    - Smart metric pattern recognition
      - Identify patterns, causing the alerting to adapt and deviate as necessary
    - Intuitive configuration 

### Monitoring for Unused Resources
- Azure Advisor
  - Helps you follow best practices to optimize your Azure deployments
  - Useful for tracking under-utilized resources
  - Reviews resource configuration and usage telemetry to provide advice on how to improve expenses, efficiency, performance, availability, and security of your Azure resources
  - Recommendations are divided into the following 4 categories
    - Availability
    - Security
    - Costs
    - Performance

### Monitoring Spending/Report on Spending
- Azure provides a set of Billing Rest API's that give access to resource consumption and metadata information for Azure subscriptions
  - Pricing Calculator
    - Estimates in all areas of Azure
  - Billing Alert Service
    - Create alerts to send when approaching spending limits
  - Cost Analysis
    - Useful for exploring and analyzing your organization's costs
  - Customize cost views
    - 4 built-in views
      1. Accumulated costs
      2. Daily costs
      3. Cost by service
      4. Cost by resource
  - Download Reports
  - Cost Analysis Prerequisites
   - Read access to billing account, department, enrollment account, management group, subscription, or resource group

### Utilizing Log Search Query Functions
- Log Analytics
  - Helps you collect, correlate, search, and act on log and performance data generated by operating systems and applications
  - Most interactions will be through the OMS portal
    - The portal supports constructing queries to analyze collected data, customizing dashboards with graphical views, and provides additional functionality and analysis tools. 
- Connected sources 
  - Computers and other resources that generate data collected by Log Analytics
  - Data sources
    - Different kinds of data collected from each connected source
- Query
  - Log Analytics provides a query syntax to quickly retrieve and consolidate data in the repository.
  - You can create alerts based on the results of the query. 

### Viewing Alerts in Log Analytics
- Alert Management
  - Helps you view your operations manager and Log Analytics log alerts across your environment
  - Centralizes alerts and helps identify root causes

## Manage Resource Groups
### Using Azure Policies for Resource Groups
- Resource groups
  - A container for multiple resources
  - Resources need to be deployed to a new or existing resource group
- Resource group rules
  1. Resources can only exist in one resource group
  2. Resource groups cannot be renamed
  3. Resource groups can have resources of many different types called services
  4. Resource groups can have resources from many different regions
- Policies
  - Helpful for controlling what resources are allowed to be created in certain Resource Groups
  - ![alt text](images/resource_group_policies.png "Resource Group Policies")

### Configuring Resource Locks
- Two types of locks
  1. Read-only lock
    - Prevents any change to the resource
  2. Delete lock
    - Prevents the deletion of the resource
- Only Owner and User Access Administrator roles can create or delete management locks

### Configuring Resource Policies and Removing Resource Groups
- 4 methods for configuring policies
  1. CLI
  2. PowerShell
  3. Azure Portal
  4. API

### Identifying Auditing Requirements
- Use azure Security Center
  - Built-in security management system that audits Azure infrastructure as well as on-premise environments
  - Addresses the three most urgent security challenges
    1. Strengthen Security Posture
    2. Protect against threats
      - Provides useful preventative advice on how to secure your environment
      - Provides threat detection notifications
    3. Get secure faster
      - Utilizes machine learning with live data to help provide secure automated provisioning and built-in protection


### Implementing and Setting Tagging on Resource Groups
- Useful in helping with organization and tracking cost
- Each tag consists of a name-value pair
- Limitations
  - Not all resource types support tags
  - VM and VM scale sets are limited to 2048 characters for all tag names and values
  - Max 50 tags per resource
  - Tags applied to resource groups are not inherited by resources within the group
  - Tag names are limited to 512 characters while the value is limited to256
  - Can't be applied to classic resources
  - Can't contain "< > % \ ? /" characters  

### Moving Resources Across Resource Groups