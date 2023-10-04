**Level 3: Component diagram**

Next you can zoom in and decompose each container further to identify the major structural building blocks and their interactions.

The Component diagram shows how a container is made up of a number of "components", what each of those components are, their responsibilities and the technology/implementation details.

**Scope**: A single container.

**Primary elements**: Components within the container in scope.
Supporting elements: Containers (within the software system in scope) plus people and software systems directly connected to the components.

**Intended audience**: Software architects and developers.

```plantuml
@startuml
!include <C4/C4_Component>

title RSA Library

LAYOUT_LANDSCAPE()

Container(adaptor, "RSA Adaptor", "Implements product-specific marshalling of events")

System_Boundary(library, "RSA Library") {
    Container(event_filter, "Event Filter", "c class")
    Container(registration_handler, "Registration Handler", "c class")
    Container(event_handler, "Event Handler", "c class")
    Container(file_handler, "Log File Handler", "c class")
    Container_Ext(iotsdk, "Azure IoT SDK", "static library")
    Container_Ext(storagesdk, "Azure Storage SDK", "static library")
    Container_Ext(sks_adaptor, "SKS Adaptor", "static library")
}

System_Ext(sks, "Secure Key Store", "x509 Key")
System_Ext(cert_store, "Certificate Store", "Trusted CA Root")

System_Ext(rss, "Remote Services Application (RSS)", "")

Rel(adaptor, event_filter, "Uses", "")
Rel(adaptor, registration_handler, "Uses", "")
Rel(adaptor, file_handler, "Uses", "")

Rel(event_filter, event_handler, "Depends On", "")

Rel(event_handler, iotsdk, "Depends On", "")
Rel(registration_handler, iotsdk, "Depends On", "")
Rel(file_handler, iotsdk, "Depends On", "")
Rel(file_handler, storagesdk, "Depends On", "")

Rel(iotsdk, sks_adaptor, "Depends On", "")
Rel(sks_adaptor, sks, "Accesses", "")
Rel(iotsdk, cert_store, "Accesses", "")

Rel(iotsdk, rss, "Sends Telemetry", "MQTT")
Rel(iotsdk, rss, "Handles Registration", "MQTT")
Rel(storagesdk, rss, "Sends Log Files", "HTTPS")

Rel(rss, iotsdk, "Sends Commands", "MQTT")

@enduml

```