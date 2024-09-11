# RHDH-GKE


### Create cm app-config-custom
`kubectl create -f rhdh-configs/app-config-custom.yaml`

### Create pull secret and sa 
`kubectl create -f manifests/rhdh-pull-secret.yaml`


### Install helm chart on GKE

```
helm upgrade rhdh \      
  openshift-helm-charts/redhat-developer-hub \
  --version 1.2.3 \
  --values values.yaml
```

### Create GCP Service Account and GKE SA
*rhdh-db-sa*
`kubectl create -f manifests/rhdh-sa.yaml`

### Create GCS Bucket
 *rhdh-bucket*

### Add IAM Policy for GKE SA
```
gcloud storage buckets add-iam-policy-binding gs://rhdh-bucket \
    --role=roles/storage.objectAdmin \
--member=principal://iam.googleapis.com/projects/164783215070/locations/global/workloadIdentityPools/openenv-b4g6h.svc.id.goog/subject/ns/default/sa/rhdh-sa \
    --condition=None
```

### test pod to check workload identity working

`kubectl create -f manifests/test-pod.yaml`
Run `curl -H "Metadata-Flavor: Google" http://169.254.169.254/computeMetadata/v1/instance/service-accounts/default/token` on the pod 