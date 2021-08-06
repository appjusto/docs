# 1 Create Firebase project

1. Go to [Firebase Console](https://console.firebase.google.com)
2. Click on '+ Add Project'
3. Define a unique identifier considering the environment, ex: app-justo-dev
4. Go to "Usage and billing" > "Details & settings" and upgrade plan to "Blaze'

# 2 Configure Firebase Authentication backend

1. Enable Email/password sign-in method (with 'Email link (passwordless sign-in) enabled')
2. Add domains to 'Authorized domains', ex: staging.admin.appjusto.com.br staging.deeplink.appjusto.com.br
3. In 'Templates', configure 'Template language' to desired language;
4. Update Sender name, from, reply to and subect accordinly;
5. Add/update DNS entry to include TXT to validate domain;

# 3 Configure Firebase Firestore backend

1. Create database defining the region closest to most users

# 4 Enable Google Cloud Platform (GCP) APIs

1. Go to [GCP Console](https://console.cloud.google.com)
2. Select project
3. Select "API & Services" > "Library
4. Enable Google Maps Directions API, Geocoding API, Places API, Maps Javascript API, Maps SDK for Android and Maps SDK for iOS;
5. Select "Credentials" > "+ Create Credentials" > "API Key" and restrict to Google Maps Directions API (to be used in step 7 as googlemaps.apikey)
6. Select "Credentials" > "+ Create Credentials" > "API Key" and restrict to Google Maps Geocoding API, Places API, Maps Javascript API, Maps SDK for Android and Maps SDK for iOS (to be used on the frontend)

# 5 Create key for service account

1. Select "IAM & Admin" > "Service accounts"
2. Select "App Engine default service account"
3. Select "Keys"
4. Select "Add Key" > "Create new Key"
5. Select "JSON format" and save file to a local directory
6. Add an export to user's script (.bash_prfile or .zshrc) pointing to the PATH of service account key file like:

```bash
export GOOGLE_APPLICATION_CREDENTIALS="/absolute-path-to-service-account-file"
```

# 6 Clone firebase-functions repository

1. Clone repository and install dependencies:

```bash
git clone git@github.com:appjusto/admin.git
cd firebase-functions/functions
npm install
```

2. Create a file called `.firebaserc` in the root directory:

```json
{
  "projects": {
    "project-alias": "<PROJECT_ID>"
  }
}
```

# 7 Set environment variables

```bash
firebase --project app-justo-dev functions:config:set \
  algolia.appid="" \
  algolia.apikey="" \
  appjusto.env="" \
  fb.project="" \
  fb.region="" \
  fb.tasks.serviceaccountemail="" \
  fb.tasks.matchingqueuename="matching-queue" \
  fb.tasks.marketplacequeuename="marketplace-queue" \
  fb.tasks.notificationqueuename="notification-queue" \
  freshworks.apikey="" \
  googlemaps.apikey="" \
  iugu.accountid="" \
  iugu.testtoken="" \
  iugu.livetoken="" \
  iugu.hooks.secret="" \
  sendgrid.apikey="" \
  sendgrid.businesswelcome="" \
  sendgrid.businessapproved="" \
  sendgrid.businessfirstorder="" \
  sendgrid.businessverified="" \
  sendgrid.courierwelcome="" \
  sendgrid.courierrejected="" \
  sendgrid.courierfirstdelivery="" \
  sentry.dns="" \
  
```

# 8 Deploy Firebase functions

```bash
firebase deploy --project <project-id>
```

# 9 Set initial data in Firebase Firestore

```bash
npm run build
REMOTE=true npm run bootstrap
```

# 10 Clone admin repository and install dependencies

```bash
git clone git@github.com:appjusto/admin.git
cd admin && yarn install
```

# 11 Configure environment variables

Create a file named `.env.local` filling up these variables:

```bash
echo "REACT_APP_PUBLIC_URL=
REACT_APP_FIREBASE_API_KEY=
REACT_APP_GOOGLE_MAPS_API_KEY=
REACT_APP_FIREBASE_REGION=
REACT_APP_FIREBASE_PROJECT_ID=
REACT_APP_FIREBASE_MESSAGING_SENDER_ID=
REACT_APP_FIREBASE_APP_ID=
REACT_APP_FIREBASE_MEASUREMENT_ID=
REACT_APP_FIREBASE_EMULATOR=
REACT_APP_FIREBASE_EMULATOR_HOST=
REACT_APP_FIREBASE_EMULATOR_PORT=
" > .env.local
```

# 12 Configure .firebaserc

1. Create a file called `.firebaserc` in the root directory:

```json
{
  "projects": {
    "project-alias": "<PROJECT_ID>"
  },
  "targets": {
    "<PROJECT-ID>": {
      "hosting": {
        "<TARGET>": ["<SITE>"]
      }
    }
  }
}
```

# 12 Create Apps

1. Go to "Project Settings" > "General"
2. Add App for Admin;
3. Add App for both flavors for iOS, Android and Web;

# 13 Add another site to Hosting for Admin

1. Click on "Add another site" at the bottom of Hosting and name with something like appjusto-admin-staging
2. Add custom domain like staging.admin.appjusto.com.br

# 14 Deploy Admin

```bash
npm run build
firebase deploy --project <project-id>
```

# 15 Add another site to Hosting for Deeplink

1. Click on "Add another site" and name with something like appjusto-deeplink-staging
2. Add custom domain like staging.deeplink.appjusto.com.br

# 16 Configure sources in Segment

# 17 Download google-services.json

1. Go to "Project settings" > "Your Apps"
2. Select on of the Android apps
3. Click on 'google-services.json'
4. Save this file on the App root directory

# 18 Build expo

1. Create a `.env` file and configure with:

```bash
FIREBASE_API_KEY=<value from apiKey on one of the web apps>
FIREBASE_REGION=
FIREBASE_PROJECT_ID=
FIREBASE_DATABASE_NAME=
FIREBASE_MESSAGING_SENDER_ID=
FIREBASE_CONSUMER_APP_ID=
FIREBASE_CONSUMER_MEASUREMENT_ID=
FIREBASE_COURIER_APP_ID=
FIREBASE_COURIER_MEASUREMENT_ID=
GOOGLE_MAPS_API_KEY=
IUGU_ACCOUNT_ID=
ALGOLIA_APPID=
ALGOLIA_APIKEY=
SENTRY_DSN=
SENTRY_AUTH_TOKEN=
SEGMENT_CONSUMER_IOS_KEY=
SEGMENT_CONSUMER_ANDROID_KEY=
SEGMENT_COURIER_IOS_KEY=
SEGMENT_COURIER_ANDROID_KEY=
ENVIRONMENT=
```

2. Build the apps

```bash
FLAVOR=consumer expo build:android --release-channel dev
FLAVOR=courier expo build:android --release-channel <environment>
```

# 18 Configure fingerprints for Android Apps

1. Fetch android hashes

```bash
FLAVOR=courier expo fetch:android:hashes
```

2. Add fingerprints to Android Apps

# 19 Algolia

1. Create new application
2. Create indexes for business, products and fleets;
3. Create replicas for business and products;
4. Configure indexes and replicas (searchable attributes; ranking and sorting; facets, languages)

# 20 Enable Resize extension

# 21 Create Dynamic Link for deeplinks

# 22 Add permissions for service account (for backups)

```bash
gcloud projects add-iam-policy-binding app-justo-dev \
    --member serviceAccount:app-justo-dev@appspot.gserviceaccount.com \
    --role roles/datastore.importExportAdmin
gsutil iam ch serviceAccount:app-justo-dev@appspot.gserviceaccount.com:admin \
    gs://app-justo-dev-backups
```

# 24 Configuring GCP Cloud Tasks

1. [Enable Cloud Tasks API](https://console.cloud.google.com/apis/library/cloudtasks.googleapis.com/?q=cloud%20tasks)
2. Select correct project, ex: `gcloud config set project app-justo-dev`
3. Create queue:

```bash
gcloud tasks queues create matching-queue \
  --max-dispatches-per-second=500 \
  --max-concurrent-dispatches=1000 \
  --max-attempts=40 \
  --min-backoff=15s \
  --max-backoff=15s \
  --max-doublings=1 \
  --max-retry-duration=15s \
  --log-sampling-ratio=1.0
```

4. [Create service account](https://cloud.google.com/tasks/docs/creating-http-target-tasks)

4.1 Add "Cloud Tasks > Cloud Tasks Enqueuer", "Services Account > Service Account User" and "Cloud Functions > Cloud Function Invoker" roles;
4.2. Name as tasks-enqueuer and add a description to it

5. Update config and deploy task handler

4.3. Set serviceaccountemail
4.4. Deploy matchingTaskHandler
4.5. Edit permissions to remove 'allUsers' and add 'allAuthenticatedUsers' with role 'Cloud function Invoker'

gcloud projects add-iam-policy-binding matchingTaskHandler \
 --member serviceAccount:<email> \
 --role roles/cloudfunctions.invoker --region <region>

gcloud projects remove-iam-policy-binding matchingTaskHandler \
 --member allUsers \
 --role roles/cloudfunctions.invoker --region <region>

5. Set fb.tasks.serviceAccountEmail
