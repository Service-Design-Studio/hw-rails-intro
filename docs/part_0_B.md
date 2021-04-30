
## Part 0 (B): Preparation: deploy to Google App Engine

If this is your first time deploying to Google App Engine, you will need to do two things. First, sign up for a free [Google Cloud account](http://cloud.google.com). See the [Google App Engine page](https://cloud.google.com/appengine/docs/flexible/ruby) for more details.

Before you begin, make sure the following:
1. In the Google Cloud Console, on the project selector page, select or create a Google Cloud project. You can select your previous `rottenpotatoes` project from your earlier assignments.
1. Make sure that billing is enabled for your cloud project.
1. Enable the Cloud Build API.
1. Install and initialize the Cloud SDK. Make sure you run:

```sh
gcloud init
```

Now you can create a new app container
with `gcloud app create --project=[YOUR_PROJECT_ID]`.
Google Cloud will assign your app a whimsical name such as `luminous-coconut-237`; once your app is deployed, you would access it at `http://luminous-coconut-237.appspot.com`. 

If you already have an existing app, you can simply deploy:

```sh
gcloud app deploy
```

Now, you need to create Cloud SQL for PostgreSQL to be used as database. To do this follow the [guides](https://cloud.google.com/ruby/rails/using-cloudsql-postgres). Before you begin, make sure that you enable Cloud SQL Admin API.
1. [Enable Cloud SQL Admin API](https://console.cloud.google.com/flows/enableapi?apiid=sqladmin.googleapis.com)

To setup a Cloud SQL for PostgreSQL instance.
1. [Create a PostgreSQL instance](https://cloud.google.com/sql/docs/postgres/create-instance). You can name it `rails-intro-instance`.
1. [Create a database in the instance](https://cloud.google.com/sql/docs/postgres/create-manage-databases). You can name it `movie-db-production`.
1. [Set the postgres user password for the instance](https://cloud.google.com/sql/docs/postgres/create-manage-users#user-root).

Next, you need to modify your `config/database.yml`. To do this, first, you need to find out the instance connection name. Type the following:

```sh
gcloud sql instances describe rails-cloudsql-instance
```
Copy the value next to `connectionName`. Now, you can edit `database.yml` as follows.

```
production:
  adapter: postgresql
  encoding: unicode
  pool: 5
  timeout: 5000
  username: "[YOUR_POSTGRES_USERNAME]"
  password: "[YOUR_POSTGRES_PASSWORD]"
  database: "cat_list_production"
  host:   "/cloudsql/[YOUR_INSTANCE_CONNECTION_NAME]"
```

We also need to configure the Rails secret key in the `app.yaml` file. First, we need to generate a new secret key.

```sh
bundle exec bin/rails secret
```
Copy the generated secret key and put it inside the `app.yaml`.

```
entrypoint: bundle exec rackup --port $PORT
env: flex
runtime: ruby

env_variables:
  SECRET_KEY_BASE: [SECRET_KEY]
```

Lastly, we also need to put our Cloud SQL connection name inside `app.yaml`. The `app.yaml` should look something like the one below.

```
entrypoint: bundle exec rackup --port $PORT
env: flex
runtime: ruby

env_variables:
  SECRET_KEY_BASE: [SECRET_KEY]

beta_settings:
  cloud_sql_instances: [YOUR_INSTANCE_CONNECTION_NAME]
```


You can then try to deploy again.

```sh
gcloud app deploy
```

Is the app running on GAE? If you navigate to the URL that is printed at the end of the results from `gcloud app deploy` you'll get a "We're sorry, but something went wrong." error in the browser.

As with the previous assignment "Hello Rails",  The error is due to the fact that the `movies` table doesn't exist.  So, as before, run the initial
migration and import the seed data. We will use the `appengine` gem for this.

In order to use this `appengine` gem, we need to grant required permission. To do this. List your projects.
```sh
gcloud projects list
```
In the output, find the project you want to use to deploy and copy the project number.

Add a new member to your project IAM policy for the role `roles/editor` to run database migration. Replace both the `[YOUR-PROJECT-ID]` and `[PROJECT_NUMBER]` in the line below. 

```sh
gcloud projects add-iam-policy-binding [YOUR-PROJECT-ID] \
  --member=serviceAccount:[PROJECT_NUMBER]@cloudbuild.gserviceaccount.com \
  --role=roles/editor
```

Now, we are ready to migrate our database and run the seed script.

```sh
$ bundle exec rake appengine:exec -- bundle exec rake db:migrate
$ bundle exec rake appengine:exec -- bundle exec rake db:seed
```

Now you should be able to navigate to your app's URL. 

**Note:** don't proceed past this point until you are able to complete the above successfully, or you won't be able to receive a grade for this assignment!

Next: [Part 1: Sort the list of movies](part_1.md)
