
# Remote Service Agent

```plantuml
@startuml
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Component.puml

title RSA Component

System_Boundary(product_system, "MediCorp Product") {
    Container_Ext(product, "Product Software", "MediCorp Product")
    Container_Ext(sks, "Secure Key Store", "x509 Key")
    Container_Ext(cert_store, "Certificate Store", "Trusted CA Root")

    System_Boundary(rsa_system, "Remote Services Agent (RSA)") {
        Container_Ext(cci, "CCI Router", "Message Routing")
        Container(adaptor, "RSA Adaptor", "Implements product-specific marshalling of events")
        Container(library, "RSA Library", "Provides RSA Functionality")        
    }
}

System(rss, "Remote Services Application (RSS)", "")

BiRel(adaptor, library, "Uses", "")
BiRel(cci, library, "Uses", "")
Rel(library, sks, "Accesses", "")
Rel(library, cert_store, "Accesses", "")

BiRel(product, cci, "Events", "Initial Implementation")

BiRel(product, adaptor, "Events", "Alternative Implementation")

Rel(product, adaptor, "Sends Log Files", "Alternative Implementation")
Rel(product, cci, "Sends Log Files", "Alternative Implementation")

Rel(library, rss, "Sends Telemetry", "MQTT")
Rel(library, rss, "Handles Registration", "MQTT")

Rel(library, rss, "Sends Log Files", "HTTPS")
Rel(rss, library, "Sends Commands", "MQTT")

SHOW_LEGEND()

@enduml

```

## RSA Adaptor

The RSA Adaptor component is required to perform translation and marshalling of requests to and from the MediCorp Product.  This component will likely be developed by the MediCorp Product teams as they have the required low-level understanding of their Product.

## RSA Library

The RSA Library is the main implementation of the Remote Service product-side functionality.

This component is responsible for:

- Securely creating and maintaining connectivity to the RSS Component
- Transferring Telemetry messages from the MediCorp Product to the RSS Component
- Delivering Commands from the RSS Component to the MediCorp Product
- Performing Log File uploads from the MediCorp Product to the RSS Component

In Multi-node topologies (BOND, CEREBRO) the RSA Library will be responsible for managing connectivity of child devices.

In subsequent releases of Remote Services, the RSA Library will additionally provide functionality to allow for remote desktop connections.

This component will be implemented as a C/C++ library to offer flexibility to the MediCorp Product teams to deploy the component in the manner which best suited the individual Product.
