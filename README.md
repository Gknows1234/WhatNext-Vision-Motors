# WhatNext-Vision-Motors
Salesforce CRM for WhatsNext Vision Motors by Yajnesh Ande (SRM-AP University). Automates dealer assignments, prevents out-of-stock orders, and streamlines processes with Apex triggers and batch jobs. Docs cover requirements, data models, development, testing, and deployment for enhanced customer experience.
# WhatsNext Vision Motors CRM Implementation Documentation

**Author**: [Yajnesh Ande]  
**University**: [SRMAP University]  


## Overview
This project involves the development of a Salesforce CRM system for WhatsNext Vision Motors to enhance customer experience and streamline operations. The solution automates dealer assignments based on customer location, prevents orders for out-of-stock vehicles, and implements automated workflows for order status updates. Key technical components include Apex triggers for inventory validation, batch jobs for stock updates, and scheduled processes for order management.

## Objectives
The primary goal was to build an efficient ordering system that reduces errors and improves customer service. Specific objectives include:
- Automating dealer assignments based on customer proximity.
- Preventing order issues due to stock shortages.
- Automating order status updates based on inventory availability.
- Reducing staff workload through process automation.

## Phase 1: Planning and Requirement Analysis

### Business Requirements
I identified critical needs, including:
- Automated dealer assignment for orders.
- Real-time stock validation during order placement.
- Automated order status management.
- Scheduled email reminders for test drives.
The system was designed to handle bulk order processing and maintain accurate inventory data.

### Project Scope and Goals
The project focused on:
- Vehicle inventory management.
- Dealer network administration.
- Customer order processing.
- Test drive scheduling.
Goals included reducing order processing time, eliminating stock-related cancellations, and ensuring automated dealer assignments.

### Data and Security Model
I designed a data model with six core objects:
- **Vehicle__c**: Tracks vehicle details.
- **Vehicle_Dealer__c**: Manages dealer information.
- **Vehicle_Customer__c**: Stores customer data.
- **Vehicle_Order__c**: Handles order details.
- **Vehicle_Test_Drive__c**: Manages test drive schedules.
- **Vehicle_Service_Request__c**: Tracks service requests.
Each object includes relationships and security measures to ensure data integrity.

## Phase 2: Salesforce Development and Configuration

### Environment and Workflow Setup
I created a Salesforce developer org using the Salesforce Developer Program and implemented consistent development practices with clear naming conventions and structured deployment workflows.

### Custom Objects and Automation
I developed six custom objects with key fields:
- **Vehicle__c**:
  - Vehicle_Name__c
  - Vehicle_Model__c (Picklist)
  - Stock_Quantity__c
  - Price__c
  - Dealer__c (Lookup)
  - Status__c (Picklist)
- **Vehicle_Dealer__c**:
  - Dealer_Name__c
  - Dealer_Location__c
  - Dealer_Code__c
  - Phone__c
  - Email__c
- **Vehicle_Customer__c**:
  - Customer_Name__c
  - Email__c
  - Phone__c
  - Address__c
  - Preferred_Vehicle_Type__c
- **Vehicle_Order__c**:
  - Customer__c (Lookup)
  - Vehicle__c (Lookup)
  - Order_Date__c
  - Status__c (Picklist)
- **Vehicle_Test_Drive__c**:
  - Customer__c (Lookup)
  - Vehicle__c (Lookup)
  - Test_Drive_Date__c
  - Status__c (Picklist)
- **Vehicle_Service_Request__c**:
  - Customer__c (Lookup)
  - Vehicle__c (Lookup)
  - Service_Date__c
  - Issue_Description__c
  - Status__c (Picklist)

I implemented two record-triggered flows:
- **Dealer Assignment Flow**: Assigns orders to the nearest dealer for "Pending" orders.
- **Test Drive Notification Flow**: Sends email reminders one day before scheduled test drives.

### Apex Development
Key Apex components include:
- **VehicleOrderTriggerHandler**: Validates stock before order creation and updates inventory upon confirmation.
  ```apex
  public class VehicleOrderTriggerHandler {
      public static void handleBeforeInsert(List<Vehicle_Order__c> newOrders) {
          // Stock validation logic
      }
      public static void handleAfterInsert(List<Vehicle_Order__c> newOrders) {
          // Update stock quantity
      }
  }
  ```
- **VehicleOrderTrigger**: Manages before and after events for order inserts and updates.
  ```apex
  trigger VehicleOrderTrigger on Vehicle_Order__c (before insert, after insert, before update, after update) {
      if (Trigger.isBefore && Trigger.isInsert) {
          VehicleOrderTriggerHandler.handleBeforeInsert(Trigger.new);
      }
      if (Trigger.isAfter && Trigger.isInsert) {
          VehicleOrderTriggerHandler.handleAfterInsert(Trigger.new);
      }
  }
  ```
- **VehicleOrderBatch**: Processes pending orders and updates statuses based on stock availability.
  ```apex
  public class VehicleOrderBatch implements Database.Batchable<sObject> {
      public Database.QueryLocator start(Database.BatchableContext bc) {
          return Database.getQueryLocator('SELECT Id, Status__c FROM Vehicle_Order__c WHERE Status__c = \'Pending\'');
      }
      public void execute(Database.BatchableContext bc, List<Vehicle_Order__c> scope) {
          // Update order status based on stock
      }
      public void finish(Database.BatchableContext bc) {}
  }
  ```
- **VehicleOrderBatchScheduler**: Schedules the batch job to run daily at midnight.
  ```apex
  public class VehicleOrderBatchScheduler implements Schedulable {
      public void execute(SchedulableContext sc) {
          VehicleOrderBatch batch = new VehicleOrderBatch();
          Database.executeBatch(batch);
      }
  }
  ```

## Phase 3: User Interface and Experience

### Lightning App Setup
I created the "WhatsNext Vision Motors" Lightning app with navigation for all custom objects, reports, and dashboards, configured for System Administrator profiles.

### Page Layouts
I designed intuitive page layouts for each object, grouping fields logically and including related lists for better usability.

### User Management
I set up user access using System Administrator profiles with appropriate permissions for all features and objects.

### Reports and Dashboards
I developed reports for:
- Vehicle inventory tracking.
- Order status monitoring.
- Test drive scheduling.
- Service request management.
These reports support business decision-making through dashboards.

## Phase 4: Data Migration, Testing, and Security

### Data Migration
I used Salesforce’s Data Import Wizard to load data, ensuring accurate mapping and validation.

### Data Quality
I enabled field history tracking for critical fields and implemented duplicate and matching rules to maintain data quality.

### Security Model
I configured profiles, permission sets, roles, and sharing rules to control data access securely.

### Testing
I conducted comprehensive tests, including:
- Order creation with sufficient stock (Expected: Order confirmed, stock decremented).
- Order creation with insufficient stock (Expected: Error message, order blocked).
- Batch job execution (Expected: Pending orders updated to confirmed).
- Test drive reminders (Expected: Emails sent one day prior).
- Dealer assignment (Expected: Nearest dealer assigned).

## Phase 5: Deployment and Maintenance

### Deployment
 Change sets were used for deployment, with thorough testing to ensure system stability.

### Maintenance
I established monitoring for batch jobs, flows, and routine maintenance tasks like data cleanup and performance optimization.

### Documentation
This document outlines the system architecture, business processes, and technical implementations, with troubleshooting guidelines for common issues.

## Conclusion
The CRM system for WhatsNext Vision Motors successfully addresses operational challenges through automation, improving customer satisfaction with automated dealer assignments, preventing stock-related errors, and enhancing efficiency with batch processing and scheduled tasks. The system delivers measurable benefits, including improved order accuracy, reduced administrative workload, and scalable operations.

## Future Enhancements
Potential improvements include:
- Integration with external inventory systems.
- Advanced analytics for business insights.
- Customer self-service portals.
- AI-driven vehicle recommendations based on customer preferences.
