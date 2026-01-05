# GCP CLI Commands Cheat Sheet (PCA Exam Focus)

Essential command-line operations for the Google Cloud Professional Cloud Architect exam.

---

## Table of Contents
- [gcloud Configuration & Auth](#gcloud-configuration--auth)
- [Projects & Organizations](#projects--organizations)
- [Compute Engine](#compute-engine)
- [GKE (Kubernetes)](#gke-kubernetes)
- [Networking](#networking)
- [IAM & Security](#iam--security)
- [Storage (gsutil)](#storage-gsutil)
- [BigQuery (bq)](#bigquery-bq)
- [Cloud SQL](#cloud-sql)
- [Logging & Monitoring](#logging--monitoring)
- [Deployment & CI/CD](#deployment--cicd)
- [Kubernetes (kubectl)](#kubernetes-kubectl)

---

## gcloud Configuration & Auth

### Configuration Management
```bash
# List configurations
gcloud config configurations list

# Create new configuration
gcloud config configurations create [CONFIG_NAME]

# Activate configuration
gcloud config configurations activate [CONFIG_NAME]

# Set project
gcloud config set project [PROJECT_ID]

# Set region/zone
gcloud config set compute/region us-central1
gcloud config set compute/zone us-central1-a

# View current config
gcloud config list
```

### Authentication
```bash
# Authenticate with user account
gcloud auth login

# Authenticate with service account
gcloud auth activate-service-account --key-file=[KEY_FILE]

# List authenticated accounts
gcloud auth list

# Revoke credentials
gcloud auth revoke [ACCOUNT]

# Get access token (for API calls)
gcloud auth print-access-token

# Application Default Credentials
gcloud auth application-default login
```

**Exam Tip:** Use service accounts for production, user accounts for development.

---

## Projects & Organizations

### Project Management
```bash
# List projects
gcloud projects list

# Describe project (check parent org/folder)
gcloud projects describe [PROJECT_ID]

# Create project
gcloud projects create [PROJECT_ID] --name="[NAME]" --folder=[FOLDER_ID]

# Delete project
gcloud projects delete [PROJECT_ID]

# Set IAM policy
gcloud projects set-iam-policy [PROJECT_ID] policy.json

# Get IAM policy
gcloud projects get-iam-policy [PROJECT_ID]

# Add IAM member
gcloud projects add-iam-policy-binding [PROJECT_ID] \
  --member="user:email@example.com" \
  --role="roles/viewer"
```

### Organization & Folders
```bash
# List organizations
gcloud organizations list

# Describe organization
gcloud organizations describe [ORG_ID]

# List folders
gcloud resource-manager folders list --organization=[ORG_ID]

# Create folder
gcloud resource-manager folders create --display-name="[NAME]" \
  --organization=[ORG_ID]

# Move project to folder
gcloud projects move [PROJECT_ID] --folder=[FOLDER_ID]
```

**Exam Scenario:** Check project parent → `gcloud projects describe`

---

## Compute Engine

### Instance Management
```bash
# List instances
gcloud compute instances list

# Create instance
gcloud compute instances create [INSTANCE_NAME] \
  --zone=us-central1-a \
  --machine-type=e2-medium \
  --image-family=debian-11 \
  --image-project=debian-cloud \
  --boot-disk-size=10GB \
  --boot-disk-type=pd-standard \
  --tags=http-server \
  --metadata=startup-script='#!/bin/bash
    apt-get update'

# Create instance with service account
gcloud compute instances create [INSTANCE_NAME] \
  --service-account=[SA_EMAIL] \
  --scopes=cloud-platform

# Create preemptible instance
gcloud compute instances create [INSTANCE_NAME] \
  --preemptible \
  --maintenance-policy=TERMINATE

# Start/Stop/Delete instance
gcloud compute instances start [INSTANCE_NAME] --zone=us-central1-a
gcloud compute instances stop [INSTANCE_NAME] --zone=us-central1-a
gcloud compute instances delete [INSTANCE_NAME] --zone=us-central1-a

# SSH into instance
gcloud compute ssh [INSTANCE_NAME] --zone=us-central1-a

# Copy files (SCP)
gcloud compute scp [LOCAL_FILE] [INSTANCE_NAME]:~/remote-path

# Describe instance
gcloud compute instances describe [INSTANCE_NAME] --zone=us-central1-a

# Set instance metadata
gcloud compute instances add-metadata [INSTANCE_NAME] \
  --metadata=key=value
```

### Instance Templates & Groups
```bash
# Create instance template
gcloud compute instance-templates create [TEMPLATE_NAME] \
  --machine-type=e2-medium \
  --image-family=debian-11 \
  --image-project=debian-cloud

# Create managed instance group (MIG)
gcloud compute instance-groups managed create [GROUP_NAME] \
  --template=[TEMPLATE_NAME] \
  --size=3 \
  --zone=us-central1-a

# Set autoscaling
gcloud compute instance-groups managed set-autoscaling [GROUP_NAME] \
  --max-num-replicas=10 \
  --min-num-replicas=2 \
  --target-cpu-utilization=0.6 \
  --zone=us-central1-a

# Update instance group (rolling update)
gcloud compute instance-groups managed rolling-action start-update [GROUP_NAME] \
  --version=template=[NEW_TEMPLATE] \
  --zone=us-central1-a

# List instance groups
gcloud compute instance-groups list
```

### Disks
```bash
# Create persistent disk
gcloud compute disks create [DISK_NAME] \
  --size=100GB \
  --type=pd-ssd \
  --zone=us-central1-a

# Attach disk to instance
gcloud compute instances attach-disk [INSTANCE_NAME] \
  --disk=[DISK_NAME] \
  --zone=us-central1-a

# Create snapshot
gcloud compute disks snapshot [DISK_NAME] \
  --snapshot-names=[SNAPSHOT_NAME] \
  --zone=us-central1-a

# Create disk from snapshot
gcloud compute disks create [DISK_NAME] \
  --source-snapshot=[SNAPSHOT_NAME] \
  --zone=us-central1-a
```

**Exam Tip:** Remember `--preemptible` for cost optimization, `--scopes=cloud-platform` for full API access.

---

## GKE (Kubernetes)

### Cluster Management
```bash
# Create GKE cluster
gcloud container clusters create [CLUSTER_NAME] \
  --zone=us-central1-a \
  --num-nodes=3 \
  --machine-type=e2-medium \
  --enable-autorepair \
  --enable-autoupgrade \
  --enable-autoscaling --min-nodes=1 --max-nodes=5

# Create Autopilot cluster
gcloud container clusters create-auto [CLUSTER_NAME] \
  --region=us-central1

# Create cluster with Workload Identity
gcloud container clusters create [CLUSTER_NAME] \
  --workload-pool=[PROJECT_ID].svc.id.goog

# Get cluster credentials
gcloud container clusters get-credentials [CLUSTER_NAME] \
  --zone=us-central1-a

# List clusters
gcloud container clusters list

# Describe cluster
gcloud container clusters describe [CLUSTER_NAME] --zone=us-central1-a

# Resize cluster
gcloud container clusters resize [CLUSTER_NAME] \
  --num-nodes=5 \
  --zone=us-central1-a

# Upgrade cluster
gcloud container clusters upgrade [CLUSTER_NAME] \
  --zone=us-central1-a

# Delete cluster
gcloud container clusters delete [CLUSTER_NAME] --zone=us-central1-a
```

### Node Pool Management
```bash
# Create node pool
gcloud container node-pools create [POOL_NAME] \
  --cluster=[CLUSTER_NAME] \
  --machine-type=e2-standard-4 \
  --num-nodes=3 \
  --zone=us-central1-a

# Create preemptible node pool (cost optimization)
gcloud container node-pools create [POOL_NAME] \
  --cluster=[CLUSTER_NAME] \
  --preemptible \
  --num-nodes=3

# Enable autoscaling on node pool
gcloud container node-pools update [POOL_NAME] \
  --cluster=[CLUSTER_NAME] \
  --enable-autoscaling \
  --min-nodes=1 --max-nodes=10 \
  --zone=us-central1-a

# List node pools
gcloud container node-pools list --cluster=[CLUSTER_NAME]
```

**Exam Tip:** Use Autopilot for hands-off management, Standard for custom control.

---

## Networking

### VPC & Subnets
```bash
# Create VPC
gcloud compute networks create [VPC_NAME] --subnet-mode=custom

# Create subnet
gcloud compute networks subnets create [SUBNET_NAME] \
  --network=[VPC_NAME] \
  --region=us-central1 \
  --range=10.0.0.0/24

# Expand subnet (no downtime)
gcloud compute networks subnets expand-ip-range [SUBNET_NAME] \
  --region=us-central1 \
  --prefix-length=20

# List VPCs
gcloud compute networks list

# List subnets
gcloud compute networks subnets list --network=[VPC_NAME]

# Delete VPC
gcloud compute networks delete [VPC_NAME]
```

### Firewall Rules
```bash
# Create firewall rule
gcloud compute firewall-rules create [RULE_NAME] \
  --network=[VPC_NAME] \
  --allow=tcp:80,tcp:443 \
  --source-ranges=0.0.0.0/0 \
  --target-tags=web-server

# Create egress rule (deny)
gcloud compute firewall-rules create [RULE_NAME] \
  --network=[VPC_NAME] \
  --direction=EGRESS \
  --action=DENY \
  --rules=all \
  --destination-ranges=0.0.0.0/0

# List firewall rules
gcloud compute firewall-rules list

# Describe firewall rule
gcloud compute firewall-rules describe [RULE_NAME]

# Delete firewall rule
gcloud compute firewall-rules delete [RULE_NAME]
```

### Cloud NAT
```bash
# Create Cloud Router (prerequisite for NAT)
gcloud compute routers create [ROUTER_NAME] \
  --network=[VPC_NAME] \
  --region=us-central1

# Create Cloud NAT
gcloud compute routers nats create [NAT_NAME] \
  --router=[ROUTER_NAME] \
  --region=us-central1 \
  --auto-allocate-nat-external-ips \
  --nat-all-subnet-ip-ranges

# List Cloud NAT gateways
gcloud compute routers nats list --router=[ROUTER_NAME] --region=us-central1
```

### Load Balancing
```bash
# Create HTTP health check
gcloud compute health-checks create http [HEALTH_CHECK_NAME] \
  --port=80 \
  --request-path=/health

# Create backend service
gcloud compute backend-services create [BACKEND_SERVICE_NAME] \
  --protocol=HTTP \
  --health-checks=[HEALTH_CHECK_NAME] \
  --global

# Add backend to service
gcloud compute backend-services add-backend [BACKEND_SERVICE_NAME] \
  --instance-group=[MIG_NAME] \
  --instance-group-zone=us-central1-a \
  --global

# Create URL map
gcloud compute url-maps create [URL_MAP_NAME] \
  --default-service=[BACKEND_SERVICE_NAME]

# Create HTTP proxy
gcloud compute target-http-proxies create [PROXY_NAME] \
  --url-map=[URL_MAP_NAME]

# Create forwarding rule
gcloud compute forwarding-rules create [RULE_NAME] \
  --global \
  --target-http-proxy=[PROXY_NAME] \
  --ports=80
```

### VPN
```bash
# Create VPN gateway
gcloud compute vpn-gateways create [VPN_GW_NAME] \
  --network=[VPC_NAME] \
  --region=us-central1

# Create VPN tunnel
gcloud compute vpn-tunnels create [TUNNEL_NAME] \
  --peer-address=[PEER_IP] \
  --shared-secret=[SECRET] \
  --target-vpn-gateway=[VPN_GW_NAME] \
  --region=us-central1
```

**Exam Tip:** Use `--target-tags` for firewall rules, Cloud NAT for private VM internet access.

---

## IAM & Security

### Service Accounts
```bash
# Create service account
gcloud iam service-accounts create [SA_NAME] \
  --display-name="[DISPLAY_NAME]"

# List service accounts
gcloud iam service-accounts list

# Grant role to service account
gcloud projects add-iam-policy-binding [PROJECT_ID] \
  --member="serviceAccount:[SA_EMAIL]" \
  --role="roles/storage.objectViewer"

# Create service account key (avoid in production!)
gcloud iam service-accounts keys create key.json \
  --iam-account=[SA_EMAIL]

# List service account keys
gcloud iam service-accounts keys list \
  --iam-account=[SA_EMAIL]

# Delete service account key
gcloud iam service-accounts keys delete [KEY_ID] \
  --iam-account=[SA_EMAIL]

# Impersonate service account
gcloud compute instances list \
  --impersonate-service-account=[SA_EMAIL]
```

### IAM Policy Management
```bash
# Get IAM policy
gcloud projects get-iam-policy [PROJECT_ID]

# Add IAM binding
gcloud projects add-iam-policy-binding [PROJECT_ID] \
  --member="user:email@example.com" \
  --role="roles/editor"

# Remove IAM binding
gcloud projects remove-iam-policy-binding [PROJECT_ID] \
  --member="user:email@example.com" \
  --role="roles/editor"

# Set IAM policy from file
gcloud projects set-iam-policy [PROJECT_ID] policy.json

# Test IAM permissions
gcloud projects get-iam-policy [PROJECT_ID] \
  --flatten="bindings[].members" \
  --filter="bindings.role:roles/owner"
```

### Cloud KMS
```bash
# Create key ring
gcloud kms keyrings create [KEYRING_NAME] \
  --location=global

# Create crypto key
gcloud kms keys create [KEY_NAME] \
  --keyring=[KEYRING_NAME] \
  --location=global \
  --purpose=encryption

# Encrypt file
gcloud kms encrypt \
  --key=[KEY_NAME] \
  --keyring=[KEYRING_NAME] \
  --location=global \
  --plaintext-file=file.txt \
  --ciphertext-file=file.txt.enc

# Decrypt file
gcloud kms decrypt \
  --key=[KEY_NAME] \
  --keyring=[KEYRING_NAME] \
  --location=global \
  --ciphertext-file=file.txt.enc \
  --plaintext-file=file.txt
```

**Exam Tip:** Avoid service account keys; use Workload Identity or IAM impersonation.

---

## Storage (gsutil)

### Bucket Operations
```bash
# Create bucket
gsutil mb -l us-central1 -c STANDARD gs://[BUCKET_NAME]

# Create multi-region bucket
gsutil mb -l us -c STANDARD gs://[BUCKET_NAME]

# List buckets
gsutil ls

# List bucket contents
gsutil ls gs://[BUCKET_NAME]
gsutil ls -r gs://[BUCKET_NAME]/**  # Recursive

# Delete bucket
gsutil rb gs://[BUCKET_NAME]
```

### Object Operations
```bash
# Copy file to bucket
gsutil cp file.txt gs://[BUCKET_NAME]/

# Copy recursively
gsutil cp -r local-dir gs://[BUCKET_NAME]/

# Copy from bucket to local
gsutil cp gs://[BUCKET_NAME]/file.txt .

# Move/rename object
gsutil mv gs://[BUCKET_NAME]/old.txt gs://[BUCKET_NAME]/new.txt

# Delete object
gsutil rm gs://[BUCKET_NAME]/file.txt
gsutil rm -r gs://[BUCKET_NAME]/folder/  # Recursive

# Sync directory with bucket
gsutil rsync -r local-dir gs://[BUCKET_NAME]/
```

### Bucket Configuration
```bash
# Set bucket storage class
gsutil defstorageclass set NEARLINE gs://[BUCKET_NAME]

# Enable versioning
gsutil versioning set on gs://[BUCKET_NAME]

# Set lifecycle policy
gsutil lifecycle set lifecycle.json gs://[BUCKET_NAME]

# Example lifecycle.json:
# {
#   "lifecycle": {
#     "rule": [{
#       "action": {"type": "SetStorageClass", "storageClass": "NEARLINE"},
#       "condition": {"age": 30}
#     }]
#   }
# }

# Set CORS policy
gsutil cors set cors.json gs://[BUCKET_NAME]

# Enable uniform bucket-level access
gsutil uniformbucketlevelaccess set on gs://[BUCKET_NAME]

# Set bucket IAM policy
gsutil iam ch user:email@example.com:objectViewer gs://[BUCKET_NAME]

# Get bucket IAM policy
gsutil iam get gs://[BUCKET_NAME]
```

### Access Control
```bash
# Make object public
gsutil acl ch -u AllUsers:R gs://[BUCKET_NAME]/file.txt

# Make bucket public
gsutil iam ch allUsers:objectViewer gs://[BUCKET_NAME]

# Grant user access
gsutil acl ch -u user@example.com:READER gs://[BUCKET_NAME]/file.txt
```

### Performance
```bash
# Parallel composite uploads (large files)
gsutil -o GSUtil:parallel_composite_upload_threshold=150M cp large-file.txt gs://[BUCKET_NAME]/

# Set object metadata
gsutil setmeta -h "Content-Type:application/json" gs://[BUCKET_NAME]/file.json

# View object metadata
gsutil stat gs://[BUCKET_NAME]/file.txt
```

**Exam Tip:** Use lifecycle policies for cost optimization, versioning for data protection.

---

## BigQuery (bq)

### Dataset Operations
```bash
# Create dataset
bq mk --dataset --location=us [PROJECT_ID]:[DATASET_NAME]

# List datasets
bq ls

# Describe dataset
bq show [DATASET_NAME]

# Delete dataset
bq rm -r -f [DATASET_NAME]
```

### Table Operations
```bash
# Create table from schema
bq mk --table [DATASET].[TABLE] schema.json

# Load data from Cloud Storage
bq load --source_format=CSV \
  [DATASET].[TABLE] \
  gs://[BUCKET_NAME]/data.csv \
  schema.json

# Load data with autodetect schema
bq load --autodetect \
  --source_format=CSV \
  [DATASET].[TABLE] \
  gs://[BUCKET_NAME]/data.csv

# List tables
bq ls [DATASET_NAME]

# Show table schema
bq show --schema [DATASET].[TABLE]

# Delete table
bq rm -t [DATASET].[TABLE]
```

### Query Operations
```bash
# Run query
bq query --use_legacy_sql=false \
  'SELECT * FROM `project.dataset.table` LIMIT 10'

# Run query and save results
bq query --destination_table=[DATASET].[TABLE] \
  --use_legacy_sql=false \
  'SELECT * FROM `project.dataset.table`'

# Run query from file
bq query --use_legacy_sql=false < query.sql

# Export query results to GCS
bq extract --destination_format=CSV \
  [DATASET].[TABLE] \
  gs://[BUCKET_NAME]/export.csv
```

### Data Transfer
```bash
# Create scheduled query
bq mk --transfer_config \
  --data_source=scheduled_query \
  --target_dataset=[DATASET] \
  --display_name="Daily Aggregation" \
  --params='{"query":"SELECT * FROM dataset.table","destination_table_name_template":"output_{run_date}"}'

# List transfer configs
bq ls --transfer_config --transfer_location=us
```

**Exam Tip:** Use partitioning and clustering for cost optimization, scheduled queries for automation.

---

## Cloud SQL

### Instance Management
```bash
# Create Cloud SQL instance (PostgreSQL)
gcloud sql instances create [INSTANCE_NAME] \
  --database-version=POSTGRES_14 \
  --tier=db-f1-micro \
  --region=us-central1

# Create MySQL instance
gcloud sql instances create [INSTANCE_NAME] \
  --database-version=MYSQL_8_0 \
  --tier=db-n1-standard-1 \
  --region=us-central1

# List instances
gcloud sql instances list

# Describe instance
gcloud sql instances describe [INSTANCE_NAME]

# Delete instance
gcloud sql instances delete [INSTANCE_NAME]

# Start/stop instance
gcloud sql instances patch [INSTANCE_NAME] --activation-policy=ALWAYS
gcloud sql instances patch [INSTANCE_NAME] --activation-policy=NEVER
```

### Database Operations
```bash
# Create database
gcloud sql databases create [DATABASE_NAME] \
  --instance=[INSTANCE_NAME]

# List databases
gcloud sql databases list --instance=[INSTANCE_NAME]

# Delete database
gcloud sql databases delete [DATABASE_NAME] \
  --instance=[INSTANCE_NAME]
```

### User Management
```bash
# Create user
gcloud sql users create [USERNAME] \
  --instance=[INSTANCE_NAME] \
  --password=[PASSWORD]

# List users
gcloud sql users list --instance=[INSTANCE_NAME]

# Set user password
gcloud sql users set-password [USERNAME] \
  --instance=[INSTANCE_NAME] \
  --password=[NEW_PASSWORD]
```

### Backup & Recovery
```bash
# Create on-demand backup
gcloud sql backups create --instance=[INSTANCE_NAME]

# List backups
gcloud sql backups list --instance=[INSTANCE_NAME]

# Restore from backup
gcloud sql backups restore [BACKUP_ID] \
  --backup-instance=[SOURCE_INSTANCE] \
  --backup-id=[BACKUP_ID]

# Export database
gcloud sql export sql [INSTANCE_NAME] \
  gs://[BUCKET_NAME]/export.sql \
  --database=[DATABASE_NAME]

# Import database
gcloud sql import sql [INSTANCE_NAME] \
  gs://[BUCKET_NAME]/import.sql \
  --database=[DATABASE_NAME]
```

**Exam Tip:** Use Cloud SQL Proxy for secure connections, enable automated backups.

---

## Logging & Monitoring

### Cloud Logging
```bash
# Read logs
gcloud logging read "resource.type=gce_instance" --limit=10

# Read logs with filter
gcloud logging read \
  "resource.type=gce_instance AND severity=ERROR" \
  --limit=50 \
  --format=json

# Read logs for specific time range
gcloud logging read \
  "resource.type=gce_instance" \
  --freshness=1h

# Create log sink
gcloud logging sinks create [SINK_NAME] \
  storage.googleapis.com/[BUCKET_NAME] \
  --log-filter='resource.type=gce_instance'

# List sinks
gcloud logging sinks list

# Delete sink
gcloud logging sinks delete [SINK_NAME]
```

### Cloud Monitoring
```bash
# List metrics
gcloud monitoring metric-descriptors list

# Create uptime check
gcloud monitoring uptime create [CHECK_NAME] \
  --resource-type=uptime-url \
  --host=[HOSTNAME] \
  --path=/

# List alert policies
gcloud alpha monitoring policies list

# Create alert policy (from YAML)
gcloud alpha monitoring policies create --policy-from-file=policy.yaml
```

**Exam Tip:** Use log sinks to BigQuery for long-term analysis, Cloud Storage for archival.

---

## Deployment & CI/CD

### Cloud Build
```bash
# Submit build
gcloud builds submit --config=cloudbuild.yaml .

# Submit build with substitutions
gcloud builds submit --config=cloudbuild.yaml \
  --substitutions=_IMAGE_NAME=myapp,_TAG=v1.0

# List builds
gcloud builds list

# View build logs
gcloud builds log [BUILD_ID]

# Create build trigger
gcloud builds triggers create github \
  --repo-name=[REPO_NAME] \
  --repo-owner=[OWNER] \
  --branch-pattern="^main$" \
  --build-config=cloudbuild.yaml
```

### Cloud Run
```bash
# Deploy service
gcloud run deploy [SERVICE_NAME] \
  --image=gcr.io/[PROJECT_ID]/[IMAGE] \
  --platform=managed \
  --region=us-central1 \
  --allow-unauthenticated

# Deploy with environment variables
gcloud run deploy [SERVICE_NAME] \
  --image=gcr.io/[PROJECT_ID]/[IMAGE] \
  --set-env-vars="KEY1=value1,KEY2=value2"

# Deploy with VPC connector (Direct VPC Egress)
gcloud run deploy [SERVICE_NAME] \
  --image=gcr.io/[PROJECT_ID]/[IMAGE] \
  --network=[VPC_NAME] \
  --subnet=[SUBNET_NAME]

# List services
gcloud run services list

# Describe service
gcloud run services describe [SERVICE_NAME] --region=us-central1

# Delete service
gcloud run services delete [SERVICE_NAME] --region=us-central1

# Set IAM policy (make public)
gcloud run services add-iam-policy-binding [SERVICE_NAME] \
  --member="allUsers" \
  --role="roles/run.invoker" \
  --region=us-central1
```

### Cloud Functions
```bash
# Deploy function (Gen 2)
gcloud functions deploy [FUNCTION_NAME] \
  --gen2 \
  --runtime=python311 \
  --trigger-http \
  --entry-point=main \
  --region=us-central1 \
  --allow-unauthenticated

# Deploy function with Pub/Sub trigger
gcloud functions deploy [FUNCTION_NAME] \
  --gen2 \
  --runtime=python311 \
  --trigger-topic=[TOPIC_NAME] \
  --entry-point=main \
  --region=us-central1

# Deploy with environment variables
gcloud functions deploy [FUNCTION_NAME] \
  --gen2 \
  --set-env-vars="KEY1=value1,KEY2=value2"

# List functions
gcloud functions list

# Delete function
gcloud functions delete [FUNCTION_NAME] --region=us-central1
```

### Artifact Registry
```bash
# Create repository
gcloud artifacts repositories create [REPO_NAME] \
  --repository-format=docker \
  --location=us-central1

# Configure Docker auth
gcloud auth configure-docker us-central1-docker.pkg.dev

# Tag and push image
docker tag [IMAGE] us-central1-docker.pkg.dev/[PROJECT_ID]/[REPO_NAME]/[IMAGE]
docker push us-central1-docker.pkg.dev/[PROJECT_ID]/[REPO_NAME]/[IMAGE]

# List repositories
gcloud artifacts repositories list

# List images
gcloud artifacts docker images list us-central1-docker.pkg.dev/[PROJECT_ID]/[REPO_NAME]
```

**Exam Tip:** Use Cloud Build for CI/CD, Artifact Registry for container images (not GCR).

---

## Kubernetes (kubectl)

### Cluster & Context
```bash
# View contexts
kubectl config get-contexts

# Switch context
kubectl config use-context [CONTEXT_NAME]

# View current context
kubectl config current-context

# Set namespace
kubectl config set-context --current --namespace=[NAMESPACE]
```

### Pod Management
```bash
# List pods
kubectl get pods
kubectl get pods -n [NAMESPACE]
kubectl get pods --all-namespaces

# Describe pod
kubectl describe pod [POD_NAME]

# View logs
kubectl logs [POD_NAME]
kubectl logs -f [POD_NAME]  # Follow
kubectl logs [POD_NAME] -c [CONTAINER_NAME]  # Multi-container

# Execute command in pod
kubectl exec -it [POD_NAME] -- /bin/bash

# Delete pod
kubectl delete pod [POD_NAME]
```

### Deployment Management
```bash
# Create deployment
kubectl create deployment [NAME] --image=[IMAGE]

# Apply manifest
kubectl apply -f deployment.yaml

# List deployments
kubectl get deployments

# Scale deployment
kubectl scale deployment [NAME] --replicas=5

# Set image (rolling update)
kubectl set image deployment/[NAME] [CONTAINER]=[NEW_IMAGE]

# Rollout status
kubectl rollout status deployment/[NAME]

# Rollout history
kubectl rollout history deployment/[NAME]

# Rollback deployment
kubectl rollout undo deployment/[NAME]

# Delete deployment
kubectl delete deployment [NAME]
```

### Service Management
```bash
# Expose deployment
kubectl expose deployment [NAME] --type=LoadBalancer --port=80

# List services
kubectl get services

# Describe service
kubectl describe service [SERVICE_NAME]

# Delete service
kubectl delete service [SERVICE_NAME]
```

### ConfigMap & Secrets
```bash
# Create ConfigMap
kubectl create configmap [NAME] --from-literal=key=value

# Create ConfigMap from file
kubectl create configmap [NAME] --from-file=config.properties

# Create Secret
kubectl create secret generic [NAME] --from-literal=password=mypass

# Create Secret from file
kubectl create secret generic [NAME] --from-file=key.json

# List ConfigMaps/Secrets
kubectl get configmaps
kubectl get secrets

# Describe ConfigMap/Secret
kubectl describe configmap [NAME]
kubectl describe secret [NAME]
```

### Namespace Management
```bash
# List namespaces
kubectl get namespaces

# Create namespace
kubectl create namespace [NAME]

# Delete namespace
kubectl delete namespace [NAME]
```

### Resource Management
```bash
# Get resources
kubectl get all
kubectl get all -n [NAMESPACE]

# Get resource YAML
kubectl get deployment [NAME] -o yaml

# Edit resource
kubectl edit deployment [NAME]

# Delete resources
kubectl delete -f manifest.yaml
kubectl delete deployment,service [NAME]
```

### Troubleshooting
```bash
# Get events
kubectl get events
kubectl get events --sort-by=.metadata.creationTimestamp

# Top nodes (resource usage)
kubectl top nodes

# Top pods
kubectl top pods

# Port forward (local testing)
kubectl port-forward pod/[POD_NAME] 8080:80

# Copy files from pod
kubectl cp [POD_NAME]:/path/to/file ./local-file
```

**Exam Tip:** Know `kubectl apply`, `rollout`, `scale`, and `exec` commands well.

---

## Quick Reference: Common Exam Scenarios

| Scenario | Command |
|----------|---------|
| Check project parent (org/folder) | `gcloud projects describe [PROJECT_ID]` |
| Create preemptible VM | `gcloud compute instances create --preemptible` |
| Enable GKE Workload Identity | `gcloud container clusters create --workload-pool=[PROJECT].svc.id.goog` |
| Make Cloud Storage bucket public | `gsutil iam ch allUsers:objectViewer gs://[BUCKET]` |
| Create Cloud SQL backup | `gcloud sql backups create --instance=[INSTANCE]` |
| Deploy Cloud Run with VPC | `gcloud run deploy --network=[VPC] --subnet=[SUBNET]` |
| Get GKE credentials | `gcloud container clusters get-credentials [CLUSTER]` |
| Expand subnet (no downtime) | `gcloud compute networks subnets expand-ip-range` |
| Create service account | `gcloud iam service-accounts create [SA_NAME]` |
| Enable bucket versioning | `gsutil versioning set on gs://[BUCKET]` |
| SSH into Compute Engine | `gcloud compute ssh [INSTANCE] --zone=[ZONE]` |
| Run BigQuery query | `bq query --use_legacy_sql=false '[SQL]'` |
| View Cloud Logging logs | `gcloud logging read "resource.type=gce_instance"` |
| Scale GKE deployment | `kubectl scale deployment [NAME] --replicas=5` |
| Rollback GKE deployment | `kubectl rollout undo deployment/[NAME]` |

---

## Tips for Exam Success

1. **Practice tab completion:** `gcloud` and `kubectl` support tab completion
2. **Use `--help`:** Every command has built-in help (e.g., `gcloud compute instances create --help`)
3. **Know the defaults:** Many flags have sensible defaults (region, zone, etc.)
4. **Use `--format`:** Format output with `--format=json` or `--format=table`
5. **Understand hierarchies:** Project → Organization, VPC → Subnet, etc.
6. **Remember cost optimization:** `--preemptible`, Autopilot GKE, lifecycle policies
7. **Security first:** Service accounts over keys, private IPs, IAM least privilege

---

**Last Updated:** January 2026  
**Exam Version:** Based on current PCA exam guide (2024-2026)

**Note:** Replace placeholders in [BRACKETS] with actual values. These commands are authoritative and exam-focused.