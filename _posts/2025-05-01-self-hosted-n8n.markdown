---
layout: post
title: How I Created a Self-Hosted n8n Instance on Google Cloud Using Cloud Run and Cloud SQL
modified:
categories: 
excerpt: A detailed guide on setting up a self-hosted n8n instance in Google Cloud with Cloud Run and Cloud SQL, including Terraform scripts.
tags: [n8n, google cloud, cloud run, cloud sql, terraform]
image:
  feature:
date: 2023-10-01T12:00:00-05:00
comments: true
---

## Introduction

n8n is a powerful open-source workflow automation tool that enables users to create and customize workflows by connecting various services with minimal coding. Self-hosting n8n allows you to have complete control over your data and workflows. In this post, I will share how I set up a self-hosted n8n instance on Google Cloud using Cloud Run and Cloud SQL, while leveraging Terraform for easy infrastructure management.

## Prerequisites

To follow along, ensure you have:

- A Google Cloud account with billing enabled.
- Basic knowledge of Google Cloud services, Docker, and Terraform.
- Terraform installed on your local machine. You can find the [installation guide here](https://www.terraform.io/downloads.html).

## Setting Up Google Cloud Environment

1. **Create a Google Cloud Project**: 
   - Navigate to the [Google Cloud Console](https://console.cloud.google.com/) and create a new project.
   - Set up billing for your project if you haven't already.

2. **Enable APIs**: 
   - From the APIs & Services menu, enable the **Cloud Run** and **Cloud SQL** APIs.

## Using Terraform for Infrastructure Setup

To automate the creation of the necessary Google Cloud resources, I used Terraform. Below is a Terraform script that sets up a Cloud SQL instance.

```hcl
provider "google" {
  project = "<YOUR_PROJECT_ID>"
  region  = "us-central1"
}

resource "google_sql_database_instance" "n8n_db" {
  name             = "n8n-db"
  database_version = "POSTGRES_12"
  region          = "us-central1"

  settings {
    tier = "db-f1-micro"

    ip_configuration {
      authorized_networks {
        name = "n8n-network"
        value = "<YOUR_IP_ADDRESS>/32"
      }
      ipv4_enabled = true
    }
  }
}

resource "google_sql_user" "default" {
  name     = "<YOUR_DB_USER>"
  password = "<YOUR_DB_PASSWORD>"
  instance = google_sql_database_instance.n8n_db.name
}

resource "google_sql_database" "n8n_db" {
  name     = "n8n_db"
  instance = google_sql_database_instance.n8n_db.name
}
```

### Running Terraform

Execute the following commands in your terminal to initialize and apply the configuration:

```bash
terraform init
terraform apply
```

## Deploying n8n on Cloud Run

1. **Create a Docker Image for n8n**: The following Dockerfile can be used to build your n8n instance:

```dockerfile
FROM n8n:n8n

# Set environment variables for the database connection
ENV DB_TYPE=postgresdb
ENV DB_POSTGRESDB_HOST=<CLOUD_SQL_CONNECTION_NAME>
ENV DB_POSTGRESDB_PORT=5432
ENV DB_POSTGRESDB_DATABASE=n8n_db
ENV DB_POSTGRESDB_USER=<YOUR_DB_USER>
ENV DB_POSTGRESDB_PASSWORD=<YOUR_DB_PASSWORD>

# Allow Cloud Run to reach the external IP of Cloud SQL
ENV N8N_HOST=<YOUR_CLOUD_RUN_URL>
ENV N8N_PORT=5678

EXPOSE 5678
```

2. **Build and Deploy**: Push the Docker image to Google Container Registry and deploy to Cloud Run:

```bash
# Build the Docker image
gcloud builds submit --tag gcr.io/<YOUR_PROJECT_ID>/n8n

# Deploy to Cloud Run
gcloud run deploy n8n --image gcr.io/<YOUR_PROJECT_ID>/n8n --platform managed --region us-central1 --allow-unauthenticated
```

## Configuring n8n with Cloud SQL

To establish a connection between n8n and your Cloud SQL database:

- Use the following connection settings in the n8n configuration:

```plaintext
DB Type: Postgres
Host: <CLOUD_SQL_CONNECTION_NAME>.cloudsql.googleapis.com
Port: 5432
Database: n8n_db
User: <YOUR_DB_USER>
Password: <YOUR_DB_PASSWORD>
```

Make sure your Cloud Run instance has the appropriate IAM permissions to access the Cloud SQL instance.

## Accessing and Managing n8n

After the deployment is complete, you can access your n8n instance using the URL provided by Cloud Run. Now you can begin creating workflows and automating various tasks.

## Conclusion

Self-hosting n8n on Google Cloud with Cloud Run and Cloud SQL not only allows you to leverage the capabilities of workflow automation but also ensures that you have full control of your data. By using Terraform, I've simplified the process of setting up the infrastructure, making it repeatable and manageable.

Feel free to explore more advanced customization and optimizations to suit your needs. You can check out my [Terraform scripts](<link-to-your-scripts>) for additional reference!

## Call to Action

If you found this guide helpful, be sure to follow my blog for more tutorials on cloud automation and development practices. Happy automating!
