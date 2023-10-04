# Context

```plantuml
@startuml Context
!include <C4/C4_Context>

title Remote Services System Context

LAYOUT_TOP_DOWN()

Person_Ext(operator, "Operator", "A technician, responsible for the use of MediCorp Products")

Person(technician, "FSE", "Field Service Engineer, responsible for installation and configuration of on-site lab products and devices")
Person(support, "TSC", "Technical Support Centre")

Boundary(remoteservices, "Remote Services") {
  System(product, "Product", "MediCorp Product")
  System(rsa, "Remote Services Agent (RSA)", "Allows MediCorp Products to send logs and telemetry to RSS")
  System(rss, "Remote Services Application (RSS)", "Provides user/role management, device management, health monitoring, preventative maintenance")
}

Rel(operator, product, "Uses")
Rel(technician, product, "Configures")
Rel(support, rss, "Uses")

Rel(product, rsa, "Telemetry", "")

Rel(rsa, product, "Commands", "")

Rel_Neighbor(rss, rsa, "Sends Commands", "MQTT")
Rel_Neighbor(rsa, rss, "Sends Telemetry", "MQTT")
Rel_Neighbor(rsa, rss, "Sends Files", "HTTPS")

Rel(rss, operator, "Sends Notification", "Email/SMS")
Rel(rss, support, "Sends Notification", "Email/SMS")

SHOW_LEGEND()

@enduml

```

## Remote Services Agent (RSA)
Remote Services Agent (RSA) is the component that is deployed to the MediCorp Product, and which provides connectivity to the Remote Services Application (RSS).
 
## Remote Services Application (RSS)
Remote Services Application (RSS) is the cloud-based system component of the Remote Services solution.  It is responsible for data capture, processing, and storage.
