# hello_heart_demo

## Instructions

Navigate to the root directory and run 'docker-compose up'. This will start the Docker environment along with the LocalStack instance. LocalStack takes about 20 minutes to initialize on the first run. After the container starts, navigate to http://localhost:8888/lab and double-click the file on the left named helloHeartPipeline. From there, simply click the "Restart kernel and run all" button.

## ETL pipeline approach

For this pipeline I wrote the entire thing in a single notebook and broke the tasks out to align with the instructions provided. This Python notebook does the following:

- Reads the two local .csv files into dataframes
- Normalizes the patient address and phone number columns
- Joins the two dataframes on "patient_id"
- Hashes the PII columns
- Uploads the dataframe as parquet files to a LocalStack bucket
- Pulls the file back down to ensure data consistency

## Transformations

I transformed two columns on the patient dataframe-- I normalized phone numbers to have only numbers and extentions without country codes, and I normalized addresses to FILL THIS IN

## the de-identification process

## Docker and LocalStack

## assumptions
