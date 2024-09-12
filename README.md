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


#### TechDocs GCS 

create secret with credentials file for GCP SA

`kubectl create secret generic techdocs-credentials --from-file=credentials.json`

update the `values.yaml` to use gcs bucket with credentials:

```
- package: ./dynamic-plugins/dist/backstage-plugin-techdocs-backend-dynamic
        disabled: false
        pluginConfig:
          techdocs:
            builder: external
            generator:
              runIn: local
            publisher:
              type: 'googleGcs'
              googleGcs:
                bucketName: 'rhdh-bucket'
                # projectId: 'openenv-b4g6h' # not required if same project
                credentials: 
                  $file: "/opt/app-root/src/credentials.json"
```

ensure the GCP SA has the role (could be another role that has get storage permission)

```
gcloud projects add-iam-policy-binding openenv-b4g6h \                         
 --member "serviceAccount:rhdh-db-sa@openenv-b4g6h.iam.gserviceaccount.com" \
 --role "roles/storage.admin"
 ```