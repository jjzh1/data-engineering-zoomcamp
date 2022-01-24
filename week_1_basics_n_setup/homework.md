## Week 1 Homework

In this homework we'll prepare the environment 
and practice with terraform and SQL

## Question 1. Google Cloud SDK

Install Google Cloud SDK. What's the version you have? 

To get the version, run `gcloud --version`
# Anwser
Google Cloud SDK 369.0.0
bq 2.0.72
core 2022.01.14
gsutil 5.6


## Google Cloud account 

Create an account in Google Cloud and create a project.


## Question 2. Terraform 

Now install terraform and go to the terraform directory (`week_1_basics_n_setup/1_terraform_gcp/terraform`)

After that, run

* `terraform init`
* `terraform plan`
* `terraform apply` 

Apply the plan and copy the output to the form

# Answer

$ terraform apply
var.project
  Your GCP Project ID

  Enter a value: dtc-de-course-338713

google_storage_bucket.data-lake-bucket: Refreshing state... [id=dtc_data_lake_dtc-de-course-338713]
google_bigquery_dataset.dataset: Refreshing state... [id=projects/dtc-de-course-338713/datasets/trips_data_all]

Note: Objects have changed outside of Terraform

Terraform detected the following changes made outside of Terraform since the
last "terraform apply":

  # google_bigquery_dataset.dataset has changed
  ~ resource "google_bigquery_dataset" "dataset" {
        id                              = "projects/dtc-de-course-338713/datasets/trips_data_all"
      + labels                          = {}
        # (10 unchanged attributes hidden)

        # (4 unchanged blocks hidden)
    }

  # google_storage_bucket.data-lake-bucket has changed
  ~ resource "google_storage_bucket" "data-lake-bucket" {
        id                          = "dtc_data_lake_dtc-de-course-338713"
      + labels                      = {}
        name                        = "dtc_data_lake_dtc-de-course-338713"
        # (9 unchanged attributes hidden)


        # (2 unchanged blocks hidden)
    }


Unless you have made equivalent changes to your configuration, or ignored the
relevant attributes using ignore_changes, the following plan may include
actions to undo or respond to these changes.

─────────────────────────────────────────────────────────────────────────────

No changes. Your infrastructure matches the configuration.

Your configuration already matches the changes detected above. If you'd like
to update the Terraform state to match, create and apply a refresh-only plan:
  terraform apply -refresh-only

Apply complete! Resources: 0 added, 0 changed, 0 destroyed.


## Prepare Postgres 

Run Postgres and load data as shown in the videos

We'll use the yellow taxi trips from January 2021:

```bash
wget https://s3.amazonaws.com/nyc-tlc/trip+data/yellow_tripdata_2021-01.csv
```

Download this data and put it to Postgres

## Question 3. Count records 

How many taxi trips were there on January 15?
# Anwser
select count(*) from yellow_taxi_data where tpep_pickup_datetime::date between '2021-01-15' and '2021-01-15'
53024

## Question 4. Average

Find the largest tip for each day. 
On which day it was the largest tip in January?
# Anwser
SELECT max(tip_amount),tpep_pickup_datetime::date 
FROM yellow_taxi_data 
WHERE tpep_pickup_datetime::date between '2021-01-01' and '2021-01-31' 
GROUP BY tpep_pickup_datetime::date 
ORDER BY max(tip_amount) DESC

1140.44 "2021-01-20"


## Question 5. Most popular destination

What was the most popular destination for passengers picked up 
in central park on January 14?

Enter the district name (not id)
# Anwser
SELECT count(t."DOLocationID"), zdo."Zone" as "DropOff"


FROM yellow_taxi_trips t JOIN zones zpu 
	ON t."PULocationID" = zpu."LocationID"
	JOIN zones zdo 
	ON t."DOLocationID" = zdo."LocationID"
WHERE tpep_pickup_datetime::date between '2021-01-14' and '2021-01-14' and zpu."Zone" = 'Central Park'
GROUP BY 2
ORDER BY 1 DESC

97 "Upper East Side South"

## Question 6. 

What's the pickup-dropoff pair with the largest 
average price for a ride (calculated based on `total_amount`)?
# Anwser
SELECT AVG(total_amount) as "average",
	CONCAT(zpu."Borough", ' / ', zpu."Zone") AS "pickup_loc",
	CONCAT(zdo."Borough", ' / ', zdo."Zone") AS "dropof_loc"

FROM yellow_taxi_trips t JOIN zones zpu 
	ON t."PULocationID" = zpu."LocationID"
	JOIN zones zdo 
	ON t."DOLocationID" = zdo."LocationID"
GROUP BY "pickup_loc", "dropof_loc"
ORDER BY "average"
DESC
2292.4	"Manhattan / Alphabet City"	"Unknown / "

## Submitting the solutions

Form for sumitting (TBA)

Deadline: 24 January, 17:00 CET


