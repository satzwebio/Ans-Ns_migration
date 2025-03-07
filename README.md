# OpenShift MTC Migration Automation

## Overview
This project provides an automated solution for migrating OpenShift namespaces between clusters using Migration Toolkit for Containers (MTC) through Ansible Tower/AWX.

### Key Features
- Dynamic cluster configuration and validation
- Storage repository management
- Interactive Survey UI for migration parameters
- Automated MigPlan and MigMigration creation
- Real-time progress tracking
- Email notifications
- Multi-environment support (dev, qa, prod)

## Prerequisites

### System Requirements
- Ansible Tower/AWX 3.8+
- OpenShift 4.x clusters with MTC operator installed
- S3-compatible storage for replication repository
- SMTP server for email notifications

### Required Permissions
- Cluster-admin access on source and destination clusters
- S3 bucket read/write permissions
- Ansible Tower project admin rights

## Project Structure
```
mtc-automation/
├── inventory/
│   ├── dev/
│   │   └── group_vars/
│   ├── qa/
│   │   └── group_vars/
│   └── prod/
│       └── group_vars/
├── roles/
│   ├── cluster_validation/
│   ├── storage_validation/
│   ├── migration_plan/
│   ├── migration_execution/
│   └── email_notification/
├── playbooks/
│   └── perform_migration.yml
└── vars/
    └── main.yml
```

## Setup Instructions

### 1. Custom Credential Types Setup

#### OpenShift Cluster Credential
```yaml
name: OpenShift Cluster
description: Credentials for OpenShift cluster access
fields:
  - id: cluster_name
    type: string
    label: Cluster Name
  - id: cluster_url
    type: string
    label: API URL
  - id: cluster_token
    type: password
    label: API Token
```

#### Storage Repository Credential
```yaml
name: Storage Repository
description: S3-compatible storage credentials
fields:
  - id: storage_name
    type: string
    label: Repository Name
  - id: storage_url
    type: string
    label: S3 Endpoint URL
  - id: bucket_name
    type: string
    label: Bucket Name
  - id: access_key
    type: string
    label: Access Key
  - id: secret_key
    type: password
    label: Secret Key
```

#### Email Notification Credential
```yaml
name: SMTP Settings
description: Email notification settings
fields:
  - id: smtp_host
    type: string
    label: SMTP Host
  - id: smtp_port
    type: string
    label: SMTP Port
  - id: smtp_username
    type: string
    label: SMTP Username
  - id: smtp_password
    type: password
    label: SMTP Password
```

### 2. Ansible Tower Configuration

1. Create Project:
   - Name: MTC Migration Automation
   - SCM Type: Git
   - SCM URL: [Your repository URL]
   - SCM Branch: main

2. Create Inventory:
   - Name: MTC Migration
   - Add groups for each environment (dev, qa, prod)

3. Create Job Template:
   - Name: Perform Migration
   - Inventory: MTC Migration
   - Project: MTC Migration Automation
   - Playbook: playbooks/perform_migration.yml
   - Credentials: Add all required credential types

### 3. Survey Configuration

Configure the following survey fields:

1. Source Cluster (Dynamic Select)
   - Variable: source_cluster
   - Source: OpenShift Cluster credentials

2. Destination Cluster (Dynamic Select)
   - Variable: dest_cluster
   - Source: OpenShift Cluster credentials

3. Storage Repository (Dynamic Select)
   - Variable: storage_repo
   - Source: Storage Repository credentials

4. Source Namespace (Dynamic Select)
   - Variable: source_namespace
   - Source: API query to source cluster

5. Destination Namespace (Text)
   - Variable: dest_namespace
   - Default: {source_namespace}

6. PVC Selection (Multi-Select)
   - Variable: selected_pvcs
   - Source: API query to source namespace

7. Email Notification (Toggle)
   - Variable: enable_email
   - Type: Boolean

## Usage

1. Access Ansible Tower and locate the "Perform Migration" job template
2. Click "Launch" to start the migration process
3. Fill in the survey form:
   - Select source and destination clusters
   - Choose storage repository
   - Select source namespace
   - Confirm or modify destination namespace
   - Select PVCs to migrate
   - Enable/disable email notifications
4. Review the migration plan
5. Confirm to start migration
6. Monitor progress through Tower interface

## Validation Steps

The automation includes several validation checks:

1. Cluster Validation
   - API connectivity
   - Authentication
   - MTC operator presence
   - Required permissions

2. Storage Validation
   - S3 bucket accessibility
   - Read/write permissions
   - Storage capacity

3. Namespace Validation
   - Existence check
   - Resource compatibility
   - PVC availability

## Email Notifications

Email notifications include:
- Migration details (source/destination)
- Status and completion time
- List of migrated PVCs
- Success/failure status

## Troubleshooting

Common issues and solutions:

1. Cluster Connection Failures
   - Verify API URL
   - Check token validity
   - Confirm network connectivity

2. Storage Issues
   - Verify S3 credentials
   - Check bucket permissions
   - Confirm endpoint accessibility

3. Migration Failures
   - Check MTC operator logs
   - Verify PVC compatibility
   - Review resource constraints# Ans-Ns_migration
