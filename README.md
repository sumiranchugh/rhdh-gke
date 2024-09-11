# RDHH-GKE

Install helm chart on GKE

```
helm upgrade rhdh \      
  openshift-helm-charts/redhat-developer-hub \
  --version 1.2.3 \
  --values values.yaml
```

### Create GCP Service Account *rhdh-db-sa*

### Create GCS Bucket
 *rhdh-bucket*

### Add IAM Policy for GKE SA
```
gcloud storage buckets add-iam-policy-binding gs://rhdh-bucket \
    --role=roles/storage.objectAdmin \
--member=principal://iam.googleapis.com/projects/164783215070/locations/global/workloadIdentityPools/openenv-b4g6h.svc.id.goog/subject/ns/default/sa/rhdh-sa \
    --condition=None
```