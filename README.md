# dsio

`dsio` is a command line tool for [Google Cloud Datastore](https://cloud.google.com/datastore/). 

**This tool is under development. Please use in your own risk.**

### Features
- Bulk upsert entities in CSV and YAML file.
- Query by GQL from command line.

### Motivation

I have been developing web application which uses Datastore to store application's master data. I want to version-control master data in Datastore, and want to automatically reflect the changes to Datastore using CI service.<br>
like below:

![ci](./docs/ci.png)


# Getting Started
### Installation
```
go get -u github.com/nshmura/dsio
```

### How to authenticate
1. Create [service account](https://cloud.google.com/iam/docs/managing-service-account-keys), and download a JSON file that contains the private key. 
2. Set `DSIO_KEY_FILE` and `DSIO_PROJECT_ID` environment variables. like this:
```
export DSIO_KEY_FILE=/path/to/service_account_file.json
export DSIO_PROJECT_ID=your-gcp-project-id
```

Or specify `--key-file` and `--project-id` options:
```
$ dsio upsert --key-file <path-to-service_account_file> --project-id <project-id> ...
```

### How to connect to local Datastore Emulator

To connect to [local Datastore emnulator](https://cloud.google.com/datastore/docs/tools/datastore-emulator):
```
$(gcloud beta emulators datastore env-init)
```

For more information, please see [this document](https://cloud.google.com/datastore/docs/tools/datastore-emulator#setting_environment_variables).


# Bulk upsert entities into Datastore
To upsert entities from CSV file (e.g. upsert into `Book` kind): 
```
$ dsio upsert filename.csv -k Book
```


To upsert entities from YAML file:
```
$ dsio upsert filename.yaml
```


To specify namespace (e.g. `development` namespace):
```
$ dsio upsert filename.yaml -n development
```


### File format and Samples:
 - [CSV and TSV format](https://github.com/nshmura/dsio/wiki/CSV-and-TSV-Format)
 - [YAML format](https://github.com/nshmura/dsio/wiki/YAML-Format)
 - [CSV,TSV,YAML file samples](./samples/)


# Query entities by GQL from Datastore

To query entities by [GQL](https://cloud.google.com/datastore/docs/reference/gql_reference):
```
$ dsio query 'SELECT * FROM Book LIMIT 2'
```

Output with CSV format:
```
$ dsio query 'SELECT * FROM Book LIMIT 2' -f csv
```

To specify namespace (e.g. `production` namespace):
```
$ dsio query 'SELECT * FROM Book LIMIT 2' -n production 
```

**CAUTION:** In CSV (and TSV) format, information about types may be dropped in some case, and `noindex` value is removed.
So in some case, there is no way to restore exactly same entities in Datastore from the generated CSV.


# Options

### dsio upsert
```
$ dsio help upsert

NAME:
   dsio upsert - Bulk-upsert entities into Datastore.

USAGE:
   dsio upsert [command options] filename

OPTIONS:
   --namespace value, -n value  namespace of entities.
   --kind value, -k value       Name of destination kind.
   --format value, -f value     Format of input file. <yaml|csv|tcv>. (default: "yaml")
   --dry-run                    Skip Datastore operations.
   --batch-size value           The number of entities per one multi upsert operation. batch-size should be smaller than 500. (default: 500)
   --key-file value             name of GCP service account file. [$DSIO_KEY_FILE]
   --project-id value           Project ID of GCP. [$DSIO_PROJECT_ID]
   --verbose, -v                Make the operation more talkative.
   --no-color                   Disable color output.

```


### dsio query
```
$ dsio help query

NAME:
   dsio query - Execute a query.

USAGE:
   dsio query [command options] "[<gql_query>]"

OPTIONS:
   --namespace value, -n value  namespace of entities.
   --output value, -o value     Output filename. Entities are outputed into this file.
   --format value, -f value     Format of output. <yaml|csv|tcv>. (default: "yaml")
   --style value, -s value      Style of output. <scheme|direct|auto>. (default: "scheme")
   --page-size value            Number of entities to output at once. (default: 50)
   --key-file value             name of GCP service account file. [$DSIO_KEY_FILE]
   --project-id value           Project ID of GCP. [$DSIO_PROJECT_ID]
   --verbose, -v                Make the operation more talkative.
   --no-color                   Disable color output.
```

