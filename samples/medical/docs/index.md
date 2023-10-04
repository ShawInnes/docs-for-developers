# Purpose

The purpose of this document is to provide a high-level solution architecture for the Remote Services project.

This document will address the following:

* Architectural Principles
* Key Decisions
* Solution Design
* System Context Diagram
* Component View
* Security View
* Communications View
* Deployment View
* Single-Node Product View
* Multi-Node Product View

# Audience
This document is intended to be read and understood by:

Everybody, both technical and non-technical people, inside and outside of the software development team.

# Introduction

## Background

MediCorp Products are critical operational equipment needed to complete patient cases quickly. We require maximum uptime from them with only short, planned, downtime for preventative maintenance to minimise disruption to lab throughput, and ultimately, patient care. 

MediCorp Products and associated service are not currently meeting this critical operational need that leads to unsatisfactory unplanned downtime. Service activities are reactive, not preventative, and this requires lab staff to monitor MediCorp Products in person to be aware of malfunction. Lab staff must call in to diagnose issues and request onsite support. This creates too much opportunity for unexpected downtime and takes staff away from critical operations to work on MediCorp Product issues (workflow disruption).

## Remote Service
The remote service project aims to provide MediCorp with the capability to provide remote monitoring, preventative maintenance, and predictive maintenance.

The remote service capability will be delivered in an incremental manner, providing basic functionality initially and adding more full-featured capabilities over time.  

The full scope of the release of remote services will include features to meet the following scope requirements:
- MediCorp Product Registration
- Event-based Alarm Notification via Email and SMS
- MediCorp Product Monitoring Dashboards to show health status, connectivity, maintenance status, and MediCorp Product information such as serial number and installed location
- MediCorp Product Specific Views to show errors and warnings, logs and other pertinent information * used for diagnostic purposes
- Web-based Administration Portal
- Reports for audit, compliance, and diagnostic purposes
- Compliance with MediCorp Cybersecurity policies and any required regulations

## Scope

### Event Based Alarm Notification
- Email alerts to both internal and external users
- Configuration of who receives these notifications by instrument
- Alerts should clearly indicate 
- Instrument Time stamp
- Error / warning message
- Error Severity 
- Instrument Identification
- Instrument details 
- Model type
- Serial number

### Compliance
- Secure connection from MediCorp Product to Remote Service in Azure
- Compliance with MediCorp Cybersecurity policies
- Compliance with country specific regulations
- Email addresses will be encrypted & masked
- Backup of critical data (SQL DB) 

### Out of scope
- Region-specific data storage (data sovereignty)
- Comprehensive RSS GUI
- File Upload
- SMS Alerts

## Functional Decomposition 

### Instrument Responsibilities
- Provide a Secure Key Store containing PKI-issued x509 key, and associated trusted root/intermediate certificates
- Send all warning and error events to RSA with an Instrument Time, Event Code, Event Description and Event Severity
### Remote Service Agent (RSA) Responsibilities
- Ability to read key/cert from Secure Key Store (instrument-specific implementations)
- Secure Registration to IoT Central
- Processes events and send to IoT Central
### Remote Service Application (RSS) Responsibilities
- Process incoming events
- Enrich incoming events with Instrument Details, etc
- Classify incoming event Severity based on Instrument Type and Event Code, to determine whether to send notification
- Render notifications based on template
- Send a notification
- Archive received events for future reference

## Architectural Design Principles

The architectural design principles have been comprehensively documented elsewhere [link] and are included here in summary format only.  

## Key Decisions Affecting Architecture

- Adoption of Azure Cloud Services
- Use of Azure IoT Central
- Selection of Angular for Admin Dashboard
- Use of x509 Authentication

## System Context Diagram 

Remote Services Application (RSS) is the cloud-based system component of the Remote Services solution.  It is responsible for data capture, processing and storage.


