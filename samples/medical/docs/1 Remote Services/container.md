# Remote Services

## Remote Services Agent (RSA)
Remote Services Agent (RSA) is the component that is deployed to the MediCorp Product, and which provides connectivity to the Remote Services Application (RSS).
 
## Remote Services Application (RSS)
Remote Services Application (RSS) is the cloud-based system component of the Remote Services solution.  It is responsible for data capture, processing, and storage.


```plantuml
@startuml
!include <C4/C4_Container>

LAYOUT_WITH_LEGEND()

title Remote Services

Person(technician, "FSE", "Field Service Engineer")
Person(support, "TSC", "Technical Support Centre")
Person_Ext(operator, "Operator", "")

System_Boundary(MediCorp_product, "MediCorp Product") {
  System_Ext(product, "Product", "MediCorp Product")
  Container(rsa, "Remote Services Agent", "C++, Azure IoT SDK")
}

System_Boundary(rss_system, "Remote Services Application (RSS)") {
  Container(spa, "Single Page Application", "Angular", "Provides UI")
  Container(api, "API Application", "ASP.NET API", "Provides REST API")
  Container(notification, "Notification Service", "Azure Function", "")
  Container(processor, "Event Processor", "Azure Function", "")

  Container_Ext(iot_central, "Azure IoT Central", "SaaS", "")
  Container_Ext(stream_analytics, "Azure Stream Analytics", "SaaS", "")
  
  ContainerDb_Ext(blob_storage, "Azure Blob Storage", "SaaS", "")
  ContainerDb_Ext(db, "Database", "Azure SQL", "")
}

System_Ext(sms, "SMS Gateway", "Provides SMS Services")
System_Ext(email, "Email Gateway", "Provides Email Services")
System_Ext(aad, "Azure Active Directory", "")

Rel(technician, product, "Configures")
Rel(technician, rsa, "Configures")
Rel(support, spa, "Uses")

Rel(spa, api, "Uses")
Rel(spa, aad, "Authenticates", "OIDC")

BiRel(api, db, "Read/Write")
Rel(api, aad, "Authorises", "OIDC")

Rel(product, rsa, "Telemetry", "")
Rel(rsa, product, "Commands", "")

Rel(iot_central, processor, "Streams")
Rel(iot_central, stream_analytics, "Streams")
Rel(stream_analytics, notification, "Streams")
BiRel(processor, db, "Read/Write")

Rel(iot_central, blob_storage, "Archives")
Rel(processor, blob_storage, "Archives")

Rel(notification, sms, "Sends Notification", "HTTPS")
Rel(sms, operator, "Sends SMS Notification", "SMS")

Rel(notification, email, "Sends Notification", "HTTPS")
Rel(email, operator, "Sends Email Notification", "SMTP")

Rel_Neighbor(rsa, iot_central, "Sends Telemetry", "MQTT")
Rel_Neighbor(rsa, blob_storage, "Sends Files", "HTTPS")
Rel_Neighbor(iot_central, rsa, "Sends Commands", "MQTT")

@enduml
```