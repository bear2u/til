# Continuous Deployment with Spinnaker v1.5

3 hours

9 Credits





Rate Lab

This hands-on lab shows you how to create a continuous delivery pipeline using Google Kubernetes Engine, Google Cloud Source Repositories, Google Cloud Container Builder, and Spinnaker. After you create a sample application, you configure these services to automatically build, test, and deploy it. When you modify the application code, the changes trigger the continuous delivery pipeline to automatically rebuild, retest, and redeploy the new version.

## Objectives

- Set up your environment by launching [Google Cloud Shell](https://cloud.google.com/shell/), creating a Kubernetes Engine cluster, and configuring your identity and user management scheme.
- Download a sample application, create a Git repository then upload it to a Google Cloud Source Repository.
- Deploy Spinnaker to Kubernetes Engine using [Helm](https://github.com/kubernetes/helm).
- Build your Docker image.
- Create triggers to create Docker images when your application changes.
- Configure a Spinnaker pipeline to reliably and continuously deploy your application to Kubernetes Engine.
- Deploy a code change, triggering the pipeline, and watch it roll out to production.

## Pipeline architecture

To continuously deliver application updates to your users, you need an automated process that reliably builds, tests, and updates your software. Code changes should automatically flow through a pipeline that includes artifact creation, unit testing, functional testing, and production rollout. In some cases, you want a code update to apply to only a subset of your users, so that it is exercised realistically before you push it to your entire user base. If one of these [canary](https://martinfowler.com/bliki/CanaryRelease.html) releases proves unsatisfactory, your automated procedure must be able to quickly roll back the software changes.

![8cda078d8123f982.png](https://gcpstaging-qwiklab-website-prod.s3.amazonaws.com/bundles/assets/dd6fd5f45b5e354e1303e09c215f354c1a1d99ee72193b9ac5c7fe47436d707e.png)

With Kubernetes Engine and Spinnaker you can create a robust continuous delivery flow that helps to ensure your software is shipped as quickly as it is developed and validated. Although rapid iteration is your end goal, you must first ensure that each application revision passes through a gamut of automated validations before becoming a candidate for production rollout. When a given change has been vetted through automation, you can also validate the application manually and conduct further pre-release testing.

After your team decides the application is ready for production, one of your team members can approve it for production deployment.

### Application delivery pipeline

In this lab, you will build the continuous delivery pipeline shown in the following diagram.

![7a1802b4383b4905.png](assets/3c992d165346ae6ca21222e59c2addfb6aff8200372da5033d09f97eb63b8a47.png)

## Setup and Requirements

### Qwiklabs setup

#### What you'll need

To complete this lab, you’ll need:

- Access to a standard internet browser (Chrome browser recommended).
- Time. Note the lab’s **Completion** time in Qwiklabs. This is an estimate of the time it should take to complete all steps. Plan your schedule so you have time to complete the lab. Once you start the lab, you will not be able to pause and return later (you begin at step 1 every time you start a lab).
- The lab's **Access** time is how long your lab resources will be available. If you finish your lab with access time still available, you will be able to explore the Google Cloud Platform or work on any section of the lab that was marked "if you have time". Once the Access time runs out, your lab will end and all resources will terminate.
- You **DO NOT** need a Google Cloud Platform account or project. An account, project and associated resources are provided to you as part of this lab.
- If you already have your own GCP account, make sure you do not use it for this lab.
- If your lab prompts you to log into the console, **use only the student account provided to you by the lab**. This prevents you from incurring charges for lab activities in your personal GCP account.

#### Start your lab

When you are ready, click **Start Lab**. You can track your lab’s progress with the status bar at the top of your screen.

**Important** What is happening during this time? Your lab is spinning up GCP resources for you behind the scenes, including an account, a project, resources within the project, and permission for you to control the resources needed to run the lab. This means that instead of spending time manually setting up a project and building resources from scratch as part of your lab, you can begin learning more quickly.

#### Find Your Lab’s GCP Username and Password

To access the resources and console for this lab, locate the Connection Details panel in Qwiklabs. Here you will find the account ID and password for the account you will use to log in to the Google Cloud Platform:

![Open Google Console](https://gcpstaging-qwiklab-website-prod.s3.amazonaws.com/bundles/assets/5eaee037e6cedbf49f6a702ab8a9ef820bb8e717332946ff76a0831a6396aafc.png)

If your lab provides other resource identifiers or connection-related information, it will appear on this panel as well.

#### Log in to Google Cloud Console

Using the Qwiklabs browser tab/window or the separate browser you are using for the Qwiklabs session, copy the Username from the Connection Details panel and click the **Open Google Console** button.

You'll be asked to Choose an account. Click **Use another account**. ![Google_choose_Account](assets/c0da2885d2fec3205abd6021c643797ae3c7dae9498623b3a019668b5fa6192c-1554197742323.png)

Paste in the Username, and then the Password as prompted:

![Sign in to continue to Google Cloud Platform](assets/acff356c737df55afb91a294edfab2d88b928f5ae38cd5e40f58ae9d1481581c-1554197745572.png)

Accept the terms and conditions.

Since this is a temporary account, which you will only have to access for this one lab:

- Do not add recovery options
- Do not sign up for free trials

**Note:** You can view the list of services by clicking the GCP Navigation menu button at the top-left next to “Google Cloud Platform”.![Cloud Console Menu](https://gcpstaging-qwiklab-website-prod.s3.amazonaws.com/bundles/assets/448b955585241ad55a5a1c6d20526e831cb2fce456418c3b3ab2d1d1b26545e2.png)

### The Google Cloud Shell

### Activate Google Cloud Shell

Google Cloud Shell provides command-line access to your GCP resources.

From the GCP Console click the **Cloud Shell** icon on the top right toolbar:

![Cloud Shell Icon](https://gcpstaging-qwiklab-website-prod.s3.amazonaws.com/bundles/assets/718029dee0e562c61c14536c5a636a5bae0ef5136e9863b98160d1e06123908a.png)

Then click **START CLOUD SHELL**:

![Start Cloud Shell](https://gcpstaging-qwiklab-website-prod.s3.amazonaws.com/bundles/assets/feb5ea74b4a4f6dfac7800f39c3550364ed7a33a7ab17b6eb47cab3e65c33b13.png)

You can click **START CLOUD SHELL** immediately when the dialog comes up instead of waiting in the dialog until the Cloud Shell provisions.

It takes a few moments to provision and connects to the environment:

![Cloud Shell Terminal](https://gcpstaging-qwiklab-website-prod.s3.amazonaws.com/bundles/assets/11def2e8f4cfd6f1590f3fd825d4566658501ca87e1d5d1552aa17339050c194.png)

The Cloud Shell is a virtual machine loaded with all the development tools you’ll need. It offers a persistent 5GB home directory, and runs on the Google Cloud, greatly enhancing network performance and authentication.

Once connected to the cloud shell, you'll see that you are already authenticated and the project is set to your *PROJECT_ID*:

```
gcloud auth list
```

Output:

```output
Credentialed accounts:
 - <myaccount>@<mydomain>.com (active)
```

**Note:** `gcloud` is the powerful and unified command-line tool for Google Cloud Platform. Full documentation is available on [Google Cloud gcloud Overview](https://cloud.google.com/sdk/gcloud). It comes pre-installed on Cloud Shell and supports tab-completion.

```
gcloud config list project
```

Output:

```output
[core]
project = <PROJECT_ID>
```

## Set up your environment

Configure the infrastructure and identities required for this lab. First, you'll create a Kubernetes Engine cluster to deploy Spinnaker and the sample application.

Set the default compute zone:

```
gcloud config set compute/zone us-central1-f
```

Create a Kubernetes Engine using the Spinnaker tutorial sample application.

```
gcloud container clusters create spinnaker-tutorial --machine-type=n1-standard-2 --enable-legacy-authorization
```

This deployment will take between **five to ten minutes** to complete. You may see a warning about default scopes that you can safely ignore as it has no impact on this lab.

Once the command completes you'll have a report detailing the name, location, version, ip-address, machine-type, node version, number of nodes and status of the cluster indicating that the cluster is running.

### Configure identity and access management

Create a Cloud Identity Access Management (Cloud IAM) [service account](https://cloud.google.com/iam/docs/service-accounts) to delegate permissions to Spinnaker, allowing it to store data in Cloud Storage. Spinnaker stores its pipeline data in Cloud Storage to ensure reliability and resiliency. If your Spinnaker deployment unexpectedly fails, you can create an identical deployment in minutes with access to the same pipeline data as the original.

Upload your startup script to a Cloud Storage bucket by following these steps:

Create the service account:

```
gcloud iam service-accounts create spinnaker-storage-account \
    --display-name spinnaker-storage-account
```

Store the service account email address and your current project ID in environment variables for use in later commands:

```
export SA_EMAIL=$(gcloud iam service-accounts list \
    --filter="displayName:spinnaker-storage-account" \
    --format='value(email)')
export PROJECT=$(gcloud info --format='value(config.project)')
```

Bind the `storage.admin` role to your service account:

```
gcloud projects add-iam-policy-binding \
    $PROJECT --role roles/storage.admin --member serviceAccount:$SA_EMAIL
```

Download the service account key. In a later step, you will install Spinnaker and upload this key to Kubernetes Engine.

```
gcloud iam service-accounts keys create spinnaker-sa.json \
     --iam-account $SA_EMAIL
```

Output (do not copy)

```
created key [f36140d95582818bcecfc6fe7c3a270494a00323] of type [json] as [spinnaker-sa.json] for [spinnaker-storage-account@qwiklabs-gcp-60ca368a34737ebf.iam.gserviceaccount.com]
```

## Deploying Spinnaker using Helm

In this section, you'll use [Helm](https://github.com/kubernetes/helm) to deploy Spinnaker from the [Charts](https://github.com/kubernetes/charts) repository. Helm is a package manager you can use to configure and deploy [Kubernetes applications](http://kubeapps.com/).

### Install Helm

Download and install the Helm binary:

```
wget https://storage.googleapis.com/kubernetes-helm/helm-v2.5.0-linux-amd64.tar.gz
```

Unzip the file to your local system:

```
tar zxfv helm-v2.5.0-linux-amd64.tar.gz
cp linux-amd64/helm .
```

Initialize Helm to install Tiller, the server side of Helm, in your cluster:

```
./helm init
./helm repo update
```

Ensure that Helm is properly installed by running the following command. If Helm is correctly installed, v2.5.0 appears for both client and server.

```
./helm version
```

Output (do not copy)

```
Client: &version.Version{SemVer:"v2.5.0",
GitCommit:"012cb0ac1a1b2f888144ef5a67b8dab6c2d45be6",
GitTreeState:"clean"}Server: &version.Version{SemVer:"v2.5.0",
GitCommit:"012cb0ac1a1b2f888144ef5a67b8dab6c2d45be6",
GitTreeState:"clean"}
```

When installing and configuring Helm you may see errors related to the configuration of listener services or port binding. These relate to issues this version of Helm has with the ipv6 configuration of Cloud Shell. The errors can be ignored as they do not affect this lab.

### Configure Spinnaker

In Cloud Shell, create a bucket for Spinnaker to store its pipeline configuration:

```
export PROJECT=$(gcloud info \
    --format='value(config.project)')
export BUCKET=$PROJECT-spinnaker-config
gsutil mb -c regional -l us-central1 gs://$BUCKET
```

Create your configuration file by executing the following:

```
export SA_JSON=$(cat spinnaker-sa.json)
export PROJECT=$(gcloud info --format='value(config.project)')
export BUCKET=$PROJECT-spinnaker-config
cat > spinnaker-config.yaml <<EOF
storageBucket: $BUCKET
gcs:
  enabled: true
  project: $PROJECT
  jsonKey: '$SA_JSON'

# Disable minio the default
minio:
  enabled: false

# Configure your Docker registries here
accounts:
- name: gcr
  address: https://gcr.io
  username: _json_key
  password: '$SA_JSON'
  email: 1234@5678.com
EOF
```

### Deploy the Spinnaker chart

Use the Helm command-line interface to deploy the chart with your configuration set. This command typically takes **five to ten minutes** to complete.

```
./helm install -n cd stable/spinnaker -f spinnaker-config.yaml --timeout 600 \
    --version 0.3.1
```

You will see a warning here about a listener not getting created - you can ignore it. The installation will proceed after a few minutes.

After the command completes, run the following command to set up port forwarding to Spinnaker from Cloud Shell:

```
export DECK_POD=$(kubectl get pods --namespace default -l "component=deck" \
    -o jsonpath="{.items[0].metadata.name}")
kubectl port-forward --namespace default $DECK_POD 8080:9000 >> /dev/null &
```

To open the Spinnaker user interface, click the **Web Preview** icon at the top of the Cloud Shell window and select **Preview on port 8080**.

![bde9fe42e27656fb.png](https://gcpstaging-qwiklab-website-prod.s3.amazonaws.com/bundles/assets/6ba62726ff0694669ee2b9c921b8c0dbb27c73b600a5afba9e83c59242b16639.png)

You should see the welcome screen, followed by the Spinnaker user interface:

![8c03705c17bf3b7f.png](https://gcpstaging-qwiklab-website-prod.s3.amazonaws.com/bundles/assets/7f5d86e9c9e833060748fff73739d97375f01826158ac810278a6bba0e936df7.png)

Leave this tab open, this is where you'll access the Spinnaker user interface.

## Building the Docker image

Next you will configure Container Builder to detect changes to your application source code, build a Docker image, and then push it to Container Registry.

### Create your source code repository

Switch back to the Cloud Shell tab and download the sample application source code:

```
wget https://gke-spinnaker.storage.googleapis.com/sample-app.tgz
```

Unpack the source code:

```
tar xzfv sample-app.tgz
```

Change to the sample application source directory:

```
cd sample-app
```

Set the username and email address for your Git commits in this repository. Replace `[EMAIL_ADDRESS]` with the lab username email address, and replace `[USERNAME]`with a username you create.

```
git config --global user.email "[EMAIL_ADDRESS]"
git config --global user.name "[USERNAME]"
```

Make the initial commit to your source code repository:

```
git init
git add .
git commit -m "Initial commit"
```

Create a repository to host your code:

```
gcloud source repos create sample-app
```

You can disregard the "you may be billed for this repository" message.

```
git config credential.helper gcloud.sh
```

Add your newly created repository as remote:

```
export PROJECT=$(gcloud info --format='value(config.project)')
git remote add origin https://source.developers.google.com/p/$PROJECT/r/sample-app
```

Push your code to the new repository's master branch:

```
git push origin master
```

Check that you can see your source code in the Console by clicking **Navigation menu**, then in the **Tools** section, click **Source Repository** > **Repositories**.

### Configure your build triggers

Configure Container Builder to build and push your Docker images every time you push [Git tags](https://git-scm.com/book/en/v2/Git-Basics-Tagging) to your source repository. Container Builder automatically checks out your source code, builds the Docker image from the Dockerfile in your repository, and pushes that image to Google Cloud Container Registry.

![bcab2f07975c8776.png](https://gcpstaging-qwiklab-website-prod.s3.amazonaws.com/bundles/assets/e66b7f93774277f81f60f967f538cb4286648e1fd98a23a705643930b2c95722.png)

In the Cloud Platform Console, click **Navigation menu** > **Cloud Build** > **Build triggers**.

Click **Create trigger**.

![CloudBuild_BuildTriggers.png](https://gcpstaging-qwiklab-website-prod.s3.amazonaws.com/bundles/assets/9cf6696d577d4f981b21d7ae95cb6b57bec2fdd02281225f172053aaaf65b35a.png)

Select **Cloud Source Repository** and click **Continue**.

Click the radio button for your newly created `sample-app` repository then click **Continue**.

Set the following trigger settings:

- **Name**:`sample-app-tags`
- **Trigger type**: Tag
- **Tag (regex)**: `v.*`
- **Build configuration**: `cloudbuild.yaml`
- **cloudbuild.yaml location**: `/cloudbuild.yaml`

Click **Create trigger**.

![c982eb7bfb4d3f3a.png](https://gcpstaging-qwiklab-website-prod.s3.amazonaws.com/bundles/assets/7e94c5e8201e7e7e9731f3919ab0f8842b62bbd0c713efd00ed002f3c98a83aa.png)

From now on, whenever you push a Git tag prefixed with the letter "v" to your source code repository, Container Builder automatically builds and pushes your application as a Docker image to Container Registry.

### Build your image

Push your first image using the following steps:

Return to Cloud Shell and create a Git tag:

```
git tag v1.0.0
```

Make sure you are still in the `sample-app` directory and push the tag:

```
git push --tags
```

output (do not copy)

```
To https://source.developers.google.com/p/qwiklabs-gcp-ddf2925f84de0b16/r/sample-app
* [new tag]      v1.0.0 -> v1.0.0
```

Switch back to the Console. In **Cloud Build**, click **Build history** to check that the build has been triggered. If not, verify that the trigger was configured properly in the previous section.

![CloudBuild_BuildHistory.png](https://gcpstaging-qwiklab-website-prod.s3.amazonaws.com/bundles/assets/e21726a9ff514938487e52dbe7459a7cc1c0b1f753a268b5ef051b619673521f.png)

## Configuring your deployment pipelines

Now that your images are building automatically, you need to deploy them to the Kubernetes cluster.

You'll deploy to a scaled-down environment for testing. After the tests pass, you must manually approve the changes to deploy the code to production services.

### Create the application

In the Spinnaker tab click **Actions**, then select **Create Application**.

![e67ff7d15f630132.png](https://gcpstaging-qwiklab-website-prod.s3.amazonaws.com/bundles/assets/6bbce95e83bf7e74db30b1f2bfd70ba95ab28bea40fedf1e241c41ea5e764fc5.png)

In the **New Application** dialog, enter the following fields:

- **Name:** `sample`
- **Owner Email:** `[your lab username/email address]`

Click **Create**.

![e2437ccecb6ca7ad.png](https://gcpstaging-qwiklab-website-prod.s3.amazonaws.com/bundles/assets/79dd8446157cfaab1b86c0eff409e6f9da0a8f46a643b81f6def15430888eddb.png)

### Create service load balancers

To avoid having to enter the information manually, use the Kubernetes command-line interface to create load balancers for your services. Alternatively, you can perform this operation on the Spinnaker page.

In Cloud Shell, run the following command from the `sample-app` root directory:

```
kubectl apply -f k8s/services
```

### Create the deployment pipeline

Next, create the continuous delivery pipeline. For this lab, the pipeline is configured to detect when a Docker image with a tag prefixed with "v" has arrived in your Container Registry.

Run the following commands to upload an example pipeline to your Spinnaker instance:

```
export PROJECT=$(gcloud info --format='value(config.project)')
sed s/PROJECT/$PROJECT/g spinnaker/pipeline-deploy.json | curl -d@- -X \
    POST --header "Content-Type: application/json" --header \
    "Accept: /" http://localhost:8080/gate/pipelines
```

In the Spinnaker tab, click **Pipelines** on the top navigation bar. You can see there is 1 pipeline ready for you called Deploy.

![58f89f98a9245839.png](https://gcpstaging-qwiklab-website-prod.s3.amazonaws.com/bundles/assets/ffed90fe270564d3935907344d0649e997421c95a8e9dcd3fdf3c9954a81198b.png)

Click **Configure** and select **Deploy**.

![7ebdbe673f4a03bf.png](https://gcpstaging-qwiklab-website-prod.s3.amazonaws.com/bundles/assets/f00c5fde89fdb31153f0beb7510c7c7c562cd4ae2f72496d33803cb7d7c353df.png)

The continuous delivery pipeline configuration appears:

![dc7998f932311d94.png](https://gcpstaging-qwiklab-website-prod.s3.amazonaws.com/bundles/assets/63d77b2e3109b621a8bac0fd21ddaa38ec6a6d516281ba383cf38073cfbbe76d.png)

The configuration you just created contains a trigger to start the pipeline when you push a new Git tag containing the prefix "v". You'll next test the pipeline by running it manually, then test it by pushing a Git tag and watching the pipeline run automatically.

## Run your pipeline manually

Return to the Pipelines page by clicking **Pipelines**.

Click **Start Manual Execution** next to Deploy.

The v1.0.0 tag from the Tag drop-down list is selected by default. Click **Run**.

![9079b70a473f9a1d.png](https://gcpstaging-qwiklab-website-prod.s3.amazonaws.com/bundles/assets/226e1d1a5e02a1c3f95f25f3fd931a77d48cebd90b6a98114f7423a50b5c62e7.png)

![58cd0ca8945a0006.png](https://gcpstaging-qwiklab-website-prod.s3.amazonaws.com/bundles/assets/193b0fa3829c3a8a267be6450d72a39d9cd1b082c7a6be2b7e91302a92fe1fa6.png)

You may have to select the Deploy pipeline from the Pipeline drop-down list first if you clicked the global Start Manual Execution button at the top of the page.

After the pipeline starts, expand **Details** to see more information about the build's progress.

![Spinnaker_details.png](https://gcpstaging-qwiklab-website-prod.s3.amazonaws.com/bundles/assets/1ba8e69b9b9676bb32c65a8573c13c5014c077b4eb60e498860bfa9faed45604.png)

This section shows the status of the deployment pipeline and its steps:

- blue - currently running
- green - completed successfully
- red - failed

Click on a stage to see details about it.

After about three minutes the test phase completes and the pipeline requires manual approval to continue the deployment.

Click the yellow "person" icon and click **Continue**.

![a204a7a0640e9028.png](https://gcpstaging-qwiklab-website-prod.s3.amazonaws.com/bundles/assets/abb2a0e312c724c703525b2df0c16cda08636f963cbedc4a9654bb8a04692a7b.png)

Your rollout continues to the production frontend and backend deployments. It completes after a few minutes.

To view the app, click **Load Balancers** in the top right of the Spinnaker page.

![e26da898045569b1.png](https://gcpstaging-qwiklab-website-prod.s3.amazonaws.com/bundles/assets/758c601f1ce5098e9cefce0dd5a0f640a62f6d65312dd3f9ff2452102313210e.png)

Scroll down the list of load balancers. Under **sample-frontend-prod** click **Default**.

![9f3d80f18859a994.png](https://gcpstaging-qwiklab-website-prod.s3.amazonaws.com/bundles/assets/b19f68ebb7bff147043b276d7fe60ad4301548b9d209a332ce93a8ec38124174.png)

Scroll down the details pane on the right and copy the application's IP address by clicking the clipboard button for **Ingress**.

![45dafcf21045ac23.png](https://gcpstaging-qwiklab-website-prod.s3.amazonaws.com/bundles/assets/36c128d839c96294cc64e088884004fb628a9edee234ff1f19430385388ecc8c.png)

Paste the address into a new browser tab to view the production version of the application.

![5350848ad1354d1c.png](https://gcpstaging-qwiklab-website-prod.s3.amazonaws.com/bundles/assets/409e8dd1f3d13e8a8404102fcdd709df51dcc9d9cf06377e492c28deebfc00a2.png)

You have now manually triggered the pipeline to build, test, and deploy your application.

## Triggering your pipeline from code changes

Now test the pipeline end to end by making a code change, pushing a Git tag, and watching the pipeline run in response. By pushing a Git tag that starts with "v", you trigger Container Builder to build a new Docker image and push it to Container Registry. Spinnaker detects that the new image tag begins with "v" and triggers a pipeline to deploy the image to canaries, run tests, and roll out the same image to all pods in the deployment.

Switch back to Cloud Shell and run this code to change the color of the app from orange to blue.

```
sed -i 's/orange/blue/g' cmd/gke-info/common-service.go
```

Tag your change and push it to the source code repository.

```
git commit -a -m "Change color to blue"
git tag v1.0.1
git push --tags
```

In the Console open **Cloud Build** > **Build history** and wait a couple of minutes for the new build to appear. You may need to refresh your page.

Switch back to the Spinnaker tab and click **Pipelines** to watch the pipeline start to deploy the image. The automatically triggered pipeline will take a few minutes to appear. You may need to refresh your page. ![d006977feef1a15d.png](https://gcpstaging-qwiklab-website-prod.s3.amazonaws.com/bundles/assets/cda7b2ba1ed4310521a6b76c96d70af545b27d4deb54336a141d1a2ec0693a3e.png)

To see the canary deployments of the app, click **Load Balancers** in the top right of the Spinnaker page.

![e26da898045569b1.png](https://gcpstaging-qwiklab-website-prod.s3.amazonaws.com/bundles/assets/758c601f1ce5098e9cefce0dd5a0f640a62f6d65312dd3f9ff2452102313210e.png)

Scroll down the list of load balancers. Under **sample-frontend-canary** click **Default**.

![8b42603534ca0cd6.png](https://gcpstaging-qwiklab-website-prod.s3.amazonaws.com/bundles/assets/2adfde8772afc003318c8bda94a1be96864f1d0c9c8bf61e8dff2b1f5c372ad1.png)

Scroll down the details pane on the right and copy your application's IP address by clicking the clipboard button for **Ingress** IP.

![b9b52e0a6b289405.png](https://gcpstaging-qwiklab-website-prod.s3.amazonaws.com/bundles/assets/d97931adeca8c25db88bfff467d795bc844d3ac4d180871819d5d4784984fe8d.png)

Paste the address into a new tab in your browser. You should see the new, blue version of your application, validating that it has been successfully deployed.

After testing completes, return to the Spinnaker tab and click **Pipelines**.

Click the yellow approval button (with the person icon) and click **Continue** to approve the deployment.

When the pipeline completes, refresh your production application tab. The application now also looks blue and the **Version** field now reads `v1.0.1.`

![d786380dd6d86317.png](https://gcpstaging-qwiklab-website-prod.s3.amazonaws.com/bundles/assets/cfd822fa376241e54dacc02eaf82539da4c871a9e08d6a78fcecd16bcc6bdbc3.png)

You have successfully rolled out your application to your entire production environment!

## Roll back a change (optional)

You can roll back the change you just made by reverting your previous commit. Rolling back adds a new tag (`v1.0.2`), and pushes the tag back through the same pipeline used to deploy `v1.0.1`.

Want to check it out? Switch back to Cloud Shell and enter the following commands to roll back the change:

```
git revert v1.0.1 --no-edit
git tag v1.0.2
git push --tags
```

To verify the roll back, click on **Load Balancer**, to **sample-frontend-canary**, click **Default** and copy the Ingress IP address into a new tab.

Now your app is back to orange and you can see the version number change.

## Congratulations

You have now successfully completed the Continuous Deployment with Spinnaker lab.