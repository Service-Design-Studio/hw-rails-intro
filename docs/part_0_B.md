
## Part 0 (B): Preparation: deploy to Google Cloud Run

Before you begin, make sure the following:
1. In the Google Cloud Console, on the project selector page, select or create a Google Cloud project. You can select your previous `rottenpotatoes` project from your earlier assignments.
1. Make sure that billing is enabled for your cloud project.
1. Enable the Cloud Run, Cloud SQL, Cloud Build, Secret Manager, and Compute Engine APIs. [Enable the APIs.](https://console.cloud.google.com/flows/enableapi?apiid=run.googleapis.com,sql-component.googleapis.com,sqladmin.googleapis.com,compute.googleapis.com,cloudbuild.googleapis.com,secretmanager.googleapis.com)
1. Install and initialize the Cloud SDK. Make sure you run:

```sh
gcloud init
```

**Note:** Make sure you have done the [Hello Rails Exercise](https://github.com/Service-Design-Studio/hw-hello-rails). If not, please go through it and setup the Cloud Run accordingly, particularly the `Dockerfile` and the `cloudbuild.yaml`.

Once those steps are done, you can try to deploy the app.

```sh
gcloud builds submit
```

Now you should be able to navigate to your app's URL. 

**Note:** don't proceed past this point until you are able to complete the above successfully, or you won't be able to receive a grade for this assignment!

Next: [Part 1: Sort the list of movies](part_1.md)
