# Consuming Data in Python Applications

## Consuming Third-Party Services

### How to Use Google Sheets

##### 1. Make sure you installed the starter project as described in [01 - Starting Python Project](01_start_and_deployment.md)

##### 2. Install [Google Cloud SDK](https://cloud.google.com/sdk/docs/).

##### 3. Create credentials using a Google IAM service account and the `gcloud` command tool.

List your projects:

```
gcloud projects list
```

Create new project if you don't have one, and add a service account to it:

```
gcloud projects create PROJECTNAME
gcloud config set project PROJECTNAME
gcloud iam service-accounts create ACCOUNTNAME --display-name DISPLAYNAME
```

Enable Google Drive service for the project:

```
gcloud services list
gcloud services enable cloudapis.googleapis.com
```

Add permissions to your service account by assigning an Editor role, and generate an access key to a local credentials file, here named `googlekey.json`:

```
gcloud projects add-iam-policy-binding PROJECTNAME --member serviceAccount:ACCOUNTEMAIL --role roles/editor
gcloud iam service-accounts keys create --iam-account ACCOUNTEMAIL googlekey.json
```

Now you can authenticate `gspread` and `oauth2` to access a document that's exposed to anyone with the link or to the specific user with email `ACCOUNTEMAIL`. In the sample app, we do it the following way.

For development environment, put your `googlekey.json` into `./credentials/googlekey.json`. For any production environment, you'll need a way to put `googlekey.json` contents into an environment variable.


### Sources and Further Reading

https://www.twilio.com/blog/2017/02/an-easy-way-to-read-and-write-to-a-google-spreadsheet-in-python.html

http://jmdaignan.com/2018/02/26/metricsdash/

