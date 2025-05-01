---
layout: post
title: How I Created a Self-Hosted n8n Instance on Google Cloud Using Cloud Run and Cloud SQL
modified:
categories:
  - n8n
excerpt: n8n setup on Google Cloud
tags: [n8n, google cloud, terraform, docker, cloud run]
image:
  feature: 
date: 2023-11-21T00:00:00Z
comments: true
---

#### Introduction

n8n is a powerful open-source workflow automation tool that allows you to integrate various services and create complex workflows with minimal coding. Self-hosting n8n offers the advantage of complete control over your data, enabling customized solutions that cater specifically to your needs. In this blog post, I'll guide you through the steps I took to set up a self-hosted n8n instance on Google Cloud using Cloud Run for deployment and Cloud SQL for database management.

#### Prerequisites

Before we begin, ensure you have the following:

- **Google Cloud Account**: Make sure to have a Google Cloud account with billing enabled.
- **Basic Knowledge**: Familiarity with Docker, Terraform, and Google Cloud services will be helpful.
- **Terraform Installed**: If you haven't installed Terraform yet, check out the [official installation guide](https://www.terraform.io/downloads.html).

#### Setting Up the Google Cloud Environment

1. **Create a Google Cloud Project**:
   - Visit the [Google Cloud Console](https://console.cloud.google.com/) and create a new project.
   - Ensure that billing is enabled for this project.

2. **Enable Required APIs**:
   - In the APIs & Services section, enable the following APIs:
     - Cloud Run API
     - Cloud SQL API
     - Cloud Build API

#### Using Terraform for Infrastructure Setup

Terraform simplifies the process of managing cloud infrastructure as code. Below is a sample Terraform script to create a Cloud SQL instance that n8n will utilize.

##### Sample Terraform Script

```hcl
provider "google" {
  project = "<YOUR_PROJECT_ID>"
  region  = "us-central1"
}

resource "google_sql_database_instance" "n8n_db_instance" {
  name             = "n8n-db"
  database_version = "POSTGRES_13"
  region           = "us-central1"

  settings {
    tier = "db-f1-micro"

    ip_configuration {
      authorized_networks {
        name  = "n8n-network"
        value = "<YOUR_IP_ADDRESS>/32"
      }
      ipv4_enabled = true
    }
  }
}

resource "google_sql_user" "default" {
  name     = "<YOUR_DB_USER>"
  password = "<YOUR_DB_PASSWORD>"
  instance = google_sql_database_instance.n8n_db_instance.name
}

resource "google_sql_database" "n8n_db" {
  name     = "n8n_db"
  instance = google_sql_database_instance.n8n_db_instance.name
}
```

##### Running the Terraform Configuration

To create the resources defined in your Terraform script, execute the following commands in your terminal:

```bash
tf init
tf apply
```

#### Deploying n8n on Cloud Run

1. **Create a Docker Image for n8n**:
   Below is a basic Dockerfile configuration to set up your n8n instance:

```dockerfile
FROM n8n:n8n

# Set environment variables for connecting to Cloud SQL
ENV DB_TYPE=postgresdb
ENV DB_POSTGRESDB_HOST=<CLOUD_SQL_CONNECTION_NAME>
ENV DB_POSTGRESDB_PORT=5432
ENV DB_POSTGRESDB_DATABASE=n8n_db
ENV DB_POSTGRESDB_USER=<YOUR_DB_USER>
ENV DB_POSTGRESDB_PASSWORD=<YOUR_DB_PASSWORD>
ENV N8N_HOST=<YOUR_CLOUD_RUN_URL>
ENV N8N_PORT=5678

EXPOSE 5678
```

2. **Build and Deploy the Docker Image**:
   Use the following commands to build and deploy your Docker image to Google Cloud:

```bash
# Build the Docker image
gcloud builds submit --tag gcr.io/<YOUR_PROJECT_ID>/n8n

# Deploy to Cloud Run
gcloud run deploy n8n --image gcr.io/<YOUR_PROJECT_ID>/n8n --platform managed --region us-central1 --allow-unauthenticated
```

#### Configuring n8n with Cloud SQL

After deploying n8n on Cloud Run, you’ll need to connect it to your Cloud SQL database. Use the settings below in the n8n configuration:

- **DB Type**: Postgres
- **Host**: `<CLOUD_SQL_CONNECTION_NAME>.cloudsql.googleapis.com`
- **Port**: 5432
- **Database**: n8n_db
- **User**: `<YOUR_DB_USER>`
- **Password**: `<YOUR_DB_PASSWORD>`

Ensure that your Cloud Run service has the appropriate IAM permissions to access the Cloud SQL instance.

#### Accessing Your n8n Instance

Once your n8n instance is deployed, you can access it through the URL provided by Cloud Run. From there, you can start creating and managing your workflows seamlessly.

#### Conclusion

In this guide, you have successfully set up a self-hosted n8n instance on Google Cloud, utilizing Cloud Run for deployment and Cloud SQL for reliable data storage. This approach provides the flexibility and control necessary for effective workflow automation.

Feel free to explore my [Terraform scripts](<link-to-your-scripts>) for additional customization options.

#### Call to Action

If you found this tutorial helpful, don’t forget to follow my blog for more insights on cloud automation and development practices. Happy automating!