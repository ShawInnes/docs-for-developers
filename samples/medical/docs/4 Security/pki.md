

## Registering and Validating an Intermediate CA in IoT Central

1. An intermediate CA certificate will be downloaded from the Sectigo managed PKI solution.  This intermediate CA will be the one used to issue MediCorp Product device keys/certificates.  In the current PKI implementation, there are four potential intermediate CAs which could be responsible for issuing certificates for MediCorp Products.  
2. Create an x509 Enrollment Group in IoT Central, upload the intermediate CA certificates (not keys) to IoT Central.  This will generate a verification code.
3. Using the Verification Code from the previous step, issue a certificate with the common name matching the verification code. This must be issued by the relevant intermediate CA from step 1.
4. Repeat for all required Intermediate CAs.
5. The MediCorp Intermediate CAs are now trusted by IoT Central, and any device certificates which are signed by these intermediate CAs will also be trusted.

## Creating MediCorp Product Keys & Certificates

1. Generate leaf key/certificate whose common name must be globally unique within MediCorp, with the Serial Number and Product Model as the Common Name.  This certificate must be issued by the same verified Intermediate CA as outlined in the previous steps.
2. Install the Intermediate CA certificate & Azure IoT Central Intermediate CA certificates into the trusted CA store of the MediCorp Product.
3. Install the leaf key in the secure key store of the MediCorp Product. Install the leaf certificate in a suitable certificate store on the MediCorp Product. This key/certificate pair will then be used by RSA to authenticate to IoT Central Device Provisioning Service

## Registering/Provisioning an MediCorp Product to Remote Services

1. A component on the MediCorp instrument will read the generated MediCorp Product Key and Certificate from the secure key store.  The exact process for this is yet to be clarified, and it may vary from product to product depending on the key store implementation.
2. The RSA component will use this Key/Certificate with the Azure IoT C SDK to perform a registration request to IoT Central.  This will automatically create a device registration within IoT Central.


