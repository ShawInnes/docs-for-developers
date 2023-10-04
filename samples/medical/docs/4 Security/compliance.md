## CLOUD PLATFORM SECURITY & COMPLIANCE
The Remote Services solution has elected to use the Microsoft Azure cloud platform to host and operate the solution.  The Azure cloud platform has been independently tested and verified  against recognized standards, providing a secure and compliant operating environment for the solution.

The project will utilize the Azure Policy  feature to ensure compliance to the above regulations, as well as with any MediCorp IT security policies.  For example, Azure Policy can be used to audit and enforce that certain platform features are always enabled, such as encryption at rest and TLS version requirements.  If an operator attempts to make a change to the platform to disable one of these required features, Azure Policy will log, and block the attempt.

Azure Virtual Networks will be used to segregate services into zones, allowing for network-level security controls to be enforced in addition to application-level controls.  

All services will use Azure Private Link where possible.  Private Link provides a mechanism to securely connect between Azure platform services without exposing them to the public internet.  The platform services which should be publicly accessible, and exceptions to the previous statement, will be IoT Central (for RSA connectivity) and Azure Blob Storage (for file uploads).

All public-facing services will be protected using TLS 1.2 and certificates for these services will be issued using Azure managed certificates. This includes Azure services such as IoT Central and Blob Storage as well as MediCorp developed services such as the RSS Web App, and API.  The reason for using Azure managed certificates here is because these services can be accessed from devices not controlled by MediCorp, and therefore require the use of a registered public root certificate authority.

Authentication and authorization between services in Azure will be performed using Azure Active Directory and Managed Service Identities.  This feature provides a secure method for services to authenticate between each other without the need to share or expose passwords to any developers or operations staff.

Application and platform secrets will be stored in Azure Key Vault, for services which do not support authorization using the aforementioned Managed Service Identities.

All data will be encrypted at rest using Azure platform features.  This requirement will be enforced by using Azure Policy as stated previously.

Personally Identifiable Information (PII) and Protected Health Information (PHI) will be further protected using the data masking feature of Azure SQL Database.  This feature will perform further obfuscation of this data, preventing unauthorized users from performing read operations to the unprotected data. 

## MediCorp PRODUCT SECURITY REQUIREMENTS
MediCorp Products will require a Secure Key Store (SKS) which can securely store the MediCorp Product key used to connect to IoT Central.

MediCorp Products will require an appropriate Key and x509 Certificate to be securely installed within the Secure Key Store.  The following section articulates the details of this process.

## INTERACTION WITH PUBLIC KEY INFRASTRUCTURE
The Remote Services Application and Remote Services Agent will leverage the MediCorp PKI project to enable trusted device authentication between RSA and RSS.  

The RSA component authenticates to IoT Central by using the x509 certificate.  IoT Central knows to trust this certificate due to the fact it has been issued by a verified Intermediate CA (see below).

The RSA component may optionally verify identity of the Azure IoT Central endpoint by using Certificate Pinning, however this approach is discouraged in this situation due to the possibility of the IoT Central certificate being renewed at notice.  This would then require the Certificate Pinning configuration to be securely updated across the entire MediCorp Product suite.

Note: IoT Central and IoT Hub do not honor Certificate Revocation Lists (CRL) or Online Certificate Status Protocol (OCSP). 