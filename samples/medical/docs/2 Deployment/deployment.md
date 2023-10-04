**Deployment diagram**

## INFRASTRUCTURE AUTOMATION
The cloud infrastructure upon which the RSS runs will be deployed using Terraform.  Terraform allows for the storage of infrastructure as code artefacts in a source code repository, offering the same level of peer review tools that are used in a software development repository.

Terraform has the benefit of being used by other teams within MediCorp already and provides a common language for infrastructure automation regardless of the cloud platform used.  Terraform has been selected due to the language structure, ability to easily introduce conditional steps, and the shallow learning curve.  
There are some Azure resources which cannot be managed by Terraform at this time, Front Door is one of them.  For these scenarios either ARM, Bicep or PowerShell can be used until such time that Terraform consistently supports them.

## DEPLOYMENT AUTOMATION
Azure DevOps will be used to automate the build and deployment of application software into Azure.  Builds will automatically be triggered upon code check-in and deployments can then be triggered based upon the successful completion of a build.  Optionally, these built artefacts can also be deployed in a more manual process for more controlled environments such as production.

```plantuml
@startuml 
!include <C4/C4_Deployment>

!include <Azure/AzureCommon>
!include <Azure/AzureC4Integration>
!include <Azure/Analytics/AzureStreamAnalyticsJob>
!include <Azure/Analytics/AzureEventHub>
!include <Azure/InternetOfThings/AzureIoTCentral>
!include <Azure/Storage/AzureBlobStorage>
!include <Azure/Databases/AzureSqlDatabase>
!include <Azure/Security/AzureKeyVault>
!include <Azure/Networking/AzureFrontDoorService>
!include <Azure/Compute/AzureAppService>
!include <Azure/Compute/AzureFunction>
!include <Azure/DevOps/AzureDevOps>
!include <Azure/DevOps/AzureRepos>
!include <Azure/DevOps/AzurePipelines>

LAYOUT_LEFT_RIGHT()

title Remote Services Deployment

System_Ext(product, "Product", "MediCorp Product")
System(rsa, "Remote Services Agent", "Allows MediCorp products to send logs and telemetry to TSC")
Person(support, "TSC", "Support Person")

System_Ext(sendGrid, "SendGrid", "SMTP Email SaaS")
System_Ext(twilio, "Twilio", "SMS SaaS")

AzureFrontDoorService(frontdoor, "Front Door", "Front Door")

Deployment_Node(aue, "Azure Region", "Region") {
    Deployment_Node(asp, "App Service Plan", "Windows x3") {
        AzureAppService(api, "API Service", "")
    }

    AzureKeyVault(keyvault, "Azure KeyVault", "Standard", "")
    AzureBlobStorage(staticSite, "Static Website", "General Purpose v2, Cool, RA-GRS")

    AzureIoTCentral(iotCentral, "IoT Central", "Standard S1", "Ingress point for all telemetry, using built-in IoT Hub Routes for routing")

    AzureEventHub(eventHubTelemetry, "Device Telemetry", "Standard, 5 TUs, 4 Partitions", "In addition to the built-in 'Receive device-to-cloud messages' from IoT Hub")
    AzureFunction(eventProcessor, "Telemetry Processing", "v1, App Service plan P3v2, C#", "transform it into a different format, e.g. joining external information")
    AzureSqlDatabase(warmStorage, "Warm Storage", "2,000 RUs", "for consumption, e.g. display on a dashboard")

    AzureStreamAnalyticsJob(streamAnalytics, "Stream Analytics", "6 SUs", "apply complex queries over time periods, tolerates late (up to 21 days) and out-of-order (up to one hour) events")
    AzureFunction(notificationFunction, "Alerting", "v2, Consumption plan, JS")

    AzureBlobStorage(coldBlobStorage, "Cold Storage", "General Purpose v2, Cool, RA-GRS", "all incoming data records are archived indefinitely at low cost, and are easily accessible for batch processing")

    Rel(api, keyvault, "Uses")
}

Rel(product, rsa, "Telemetry", "")

Rel(support, frontdoor, "Accesses SPA")
Rel(frontdoor, api, "Load Balances")
Rel(frontdoor, staticSite, "Load Balances")

Rel(api, warmStorage, "Reads/Writes")

Rel(rsa, iotCentral, "Send telemetry to")

Rel(iotCentral, eventHubTelemetry, "Routes all telemetry to")
Rel_Back(iotCentral, streamAnalytics, "Analyzes each event from")
Rel(iotCentral, coldBlobStorage, "Routes all telemetry to")

Rel_Back(eventHubTelemetry, eventProcessor, "Processes each event from")
Rel(streamAnalytics, notificationFunction, "Triggers for output batches to")

Rel(eventProcessor, warmStorage, "Writes all transformed events to")

Rel(notificationFunction, sendGrid, "Uses")
Rel(notificationFunction, twilio, "Uses")

SHOW_LEGEND() 

@enduml
```