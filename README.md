# Hello Heart Demo

## Instructions

Navigate to the root directory and run 'docker-compose up' to start the Jupyter and LocalStack containers. After the container starts, navigate to http://localhost:8888/lab and double-click the file on the left named helloHeartPipeline. From there, simply click the first cell then click the "Restart kernel and run all" button.

## ETL pipeline approach

For this pipeline I wrote the entire thing in a single notebook and broke the tasks out to align with the instructions provided. This PySpark notebook does the following:

- Reads the two local .csv files into dataframes
- Normalizes the patient address and phone number columns
- Joins the two dataframes on "patient_id"
- Hashes the PII columns
- Uploads the dataframe as parquet files to a LocalStack bucket
- Pulls the file back down and displays it to ensure data consistency

## Transformations

I transformed two columns on the patient dataframe-- I normalized phone numbers to have only numbers and extentions without country codes, and I normalized addresses by stripping periods, excessive spaces and zeroes, and aligning common words to the correct abbreviations (e.g., Street -> St, Circle -> Cir, etc).

## De-identification

I chose the name, address, and phone number columns on the appointments table to hash, as those are the only fields that could uniquely identify a patient if the data were compromised. I also appended a 15-digit randomly generated salt to the input value before hashing to increase resilience against rainbow table attacks.

## Docker and LocalStack

I had ChatGPT make the initial docker-compose file. docker-compose is appropriate here because I needed to spin up and run two base containers, one for the notebook and the other for LocalStack. If I needed to further customize the notebook image or if I were hosting a custom application, that would be a use case for both a Dockerfile and a docker-compose file.

## Assumptions

While normalizing the patient phone numbers and addresses I made the assumption that they were all in American format. The regular expressions would fail if we were to introduce non-American records.

Another assumption I made was that this would be a one-time load and as such, each run wipes all the data created in LocalStack. If this were an ongoing transactional job I would have to stop truncating the bucket and start partitioning the LocalStack bucket by date.

I would also stop loading everything in LocalStack into a single datarame; if there were thousands of large part files being ingested every hour, pulling all of them into a single dataframe would run the risk of running out of memory.

## Improvements

I would introduce the use of a queue or a replication tool like DataSync or Rclone to keep track of which files have already been uploaded to LocalStack and which ones haven't. I would also implement a failure queue to retain the object keys of files that don't get ingested so we can go back and retry them later.

Some input validation at the presentation layer would serve to ensure all data is in a normalized format from the start instead of relying entirely on complex regular expressions to clean it later.
