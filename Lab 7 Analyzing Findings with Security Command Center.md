# Level-1-Security-and-Compliance-Detect-Analyze-and-Investigate-LAB7

> [PLEASE SUBSCRIBE OUR CHANNEL Nexus AI Hustle](https://www.youtube.com/@Nexus AI Hustle) **&** 
## Run in cloudshell
```cmd
gcloud pubsub topics create projects/$DEVSHELL_PROJECT_ID/topics/export-findings-pubsub-topic
gcloud pubsub subscriptions create export-findings-pubsub-topic-sub --topic=projects/$DEVSHELL_PROJECT_ID/topics/export-findings-pubsub-topic
```
> Search `Security Command Center` > Overview > Settings > Continuous Exports

> Create Pub/Sub Export > name `export-findings-pubsub` 

>Project name `your gcp id` > Topic `export-findings-pubsub-topic` > Save

```cmd
gcloud pubsub subscriptions update export-findings-pubsub-topic-sub --ack-deadline 10
gcloud compute instances create instance-1 --zone=us-central1-a \
--machine-type e2-micro \
--scopes=https://www.googleapis.com/auth/cloud-platform
gcloud alpha pubsub subscriptions pull export-findings-pubsub-topic-sub --max-messages=10
PROJECT_ID=$(gcloud config get project)
bq --location=us-central1 --apilog=/dev/null mk --dataset \
$PROJECT_ID:continuous_export_dataset
gcloud services enable securitycenter.googleapis.com
gcloud scc bqexports create scc-bq-cont-export --dataset=projects/$DEVSHELL_PROJECT_ID/datasets/continuous_export_dataset --project=$DEVSHELL_PROJECT_ID
for i in {0..2}; do
gcloud iam service-accounts create sccp-test-sa-$i;
gcloud iam service-accounts keys create /tmp/sa-key-$i.json \
--iam-account=sccp-test-sa-$i@$DEVSHELL_PROJECT_ID.iam.gserviceaccount.com;
done
bq query --apilog=/dev/null --use_legacy_sql=false  \
"SELECT finding_id,event_time,finding.category FROM continuous_export_dataset.findings"
echo "BUCKET_NAME=scc-export-bucket-$DEVSHELL_PROJECT_ID"
```
> Copy Bucket Name From Last line of terminal

> Cloud storage > Create > name `paste the name` > continue

> Region > us-central1 > create > confirm

> Search `Security Command Center` > Overview > Findings

> export > cloud storage 

>Project name `your gcp id`

>path `from the table in lab` > Format `JSONL` > EXPORT

> BigQuery > + ADD > Google cloud storage 

>path `from the table in lab` > File format `JSONL`

>Dataset	`continuous_export_dataset` > Table	`old_findings` > check 	EDIT>31.Now paste DATA SET in the following schema:CODE FROM LAB > Create

### 2nd Check will take Approx 8-10 mins 
#Congratulation you are done
