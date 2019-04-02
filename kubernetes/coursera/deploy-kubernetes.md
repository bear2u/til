# Deploying to Kubernetes v1.5

1 hour 30 minutes

1 Credit





Rate Lab

## Overview

The new goal of this lab is to get you ready for scaling and managing containers in production.

And that's where [deployments](http://kubernetes.io/docs/user-guide/deployments/) come in. Deployments are a declarative way to ensure that the number of pods running is equal to the desired number of pods specified by the user.

### Introduction to Deployments

Deployments abstract away the low level details of managing pods. They provide a single stable name that you can use to update an application. Behind the scenes, deployments rely on [ReplicaSets](https://kubernetes.io/docs/user-guide/replicasets/) to manage starting, stopping, scaling, and restarting the pods if they happen to go down for some reason. If pods need to be updated or scaled, the deployment will handle all of the details for you.

Deployments (and ReplicaSets) are powered by control loops. Control loops are a design pattern for distributed software that allows you to declaratively define your desired state and have the software implement the desired state for you based on the current state. You'll see more about how that works below.

## Setup

#### Step 1

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

![Open Google Console](assets/5eaee037e6cedbf49f6a702ab8a9ef820bb8e717332946ff76a0831a6396aafc-1553919799188.png)

If your lab provides other resource identifiers or connection-related information, it will appear on this panel as well.

#### Log in to Google Cloud Console

Using the Qwiklabs browser tab/window or the separate browser you are using for the Qwiklabs session, copy the Username from the Connection Details panel and click the **Open Google Console** button.

You'll be asked to Choose an account. Click **Use another account**. ![Google_choose_Account](assets/c0da2885d2fec3205abd6021c643797ae3c7dae9498623b3a019668b5fa6192c.png)

Paste in the Username, and then the Password as prompted:

![Sign in to continue to Google Cloud Platform](assets/acff356c737df55afb91a294edfab2d88b928f5ae38cd5e40f58ae9d1481581c.png)

Accept the terms and conditions.

Since this is a temporary account, which you will only have to access for this one lab:

- Do not add recovery options
- Do not sign up for free trials

**Note:** You can view the list of services by clicking the GCP Navigation menu button at the top-left next to “Google Cloud Platform”.![Cloud Console Menu](assets/448b955585241ad55a5a1c6d20526e831cb2fce456418c3b3ab2d1d1b26545e2.png)

#### Step 2

Make sure the following APIs are enabled in Cloud Platform Console:

- Kubernetes Engine API
- Container Registry API

On the **Navigation menu** (![Navigation menu](assets/b64830d530e08f843e60a4145b88d415dd0ee4e10a9543014586e1942ac5d166-1553919799393.png)), click **APIs & services**.

Scroll down and confirm that your APIs are enabled.

If an API is missing, click **ENABLE APIS AND SERVICES** at the top, search for the API by name, and enable it for your project.

#### Step 3

### Activate Google Cloud Shell

Google Cloud Shell provides command-line access to your GCP resources.

From the GCP Console click the **Cloud Shell** icon on the top right toolbar:

![Cloud Shell Icon](assets/718029dee0e562c61c14536c5a636a5bae0ef5136e9863b98160d1e06123908a-1553919799377.png)

Then click **START CLOUD SHELL**:

![Start Cloud Shell](assets/feb5ea74b4a4f6dfac7800f39c3550364ed7a33a7ab17b6eb47cab3e65c33b13-1553919799407.png)

You can click **START CLOUD SHELL** immediately when the dialog comes up instead of waiting in the dialog until the Cloud Shell provisions.

It takes a few moments to provision and connects to the environment:

![Cloud Shell Terminal](assets/11def2e8f4cfd6f1590f3fd825d4566658501ca87e1d5d1552aa17339050c194-1553919799583.png)

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

#### Step 4

Define your zone as a project default zone. This way you do not need to specify `--zone` parameter in gcloud commands.

```bash
gcloud config set compute/zone us-central1-a
```

Get the sample code for creating and running containers and deployments:

```bash
git clone https://github.com/googlecodelabs/orchestrate-with-kubernetes.git
```

#### Step 5

Start your Kubernetes cluster with 5 nodes.

```bash
cd orchestrate-with-kubernetes/kubernetes
```

In Cloud Shell, run the following command to start a Kubernetes cluster called bootcamp that runs 5 nodes.

```bash
gcloud container clusters create bootcamp --num-nodes 5 --scopes "https://www.googleapis.com/auth/projecthosting,storage-rw"
```

The scopes argument provides access to project hosting and Google Cloud Storage APIs that you'll use later.

It takes several minutes to create a cluster as Kubernetes Engine provisions virtual machines for you. It spins up one or more master nodes and multiple configured worker nodes. This is one of the advantages of a managed service.

## Learn About Deployment Objects

#### Step 1

Run the `explain` command in `kubectl` to tell you about the deployment object.

```bash
kubectl explain deployment
```

#### Step 2

Run the command with the `--recursive` option to see all of the fields.

```bash
kubectl explain deployment --recursive
```

#### Step 3

Use the `explain` command as you go through the lab to help you understand the structure of a deployment object and understand what the individual fields do.

```bash
kubectl explain deployment.metadata.name
```

### Create a Deployment

Create a simple deployment.

#### Step 1

Examine the deployment configuration file.

```bash
cat deployments/auth.yaml
```

`kubectl create` will create the `auth` deployment with one replica, using version 1.0.0 of the `auth` container. To scale the number of pods, you simply change the `replicas` field.

#### Step 2

Create the deployment object using `kubectl create`.

```bash
kubectl create -f deployments/auth.yaml
```

#### Step 3

Verify that it was created.

```bash
kubectl get deployments
```

#### Step 4

Kubernetes creates a ReplicaSet for the deployment.

Run the following command to verify it. You should see a ReplicaSet with a name like `auth-xxxxxxx`.

```bash
kubectl get replicasets
```

#### Step 5

Run the following command to view the pods created for your deployment. A single pod was created when the ReplicaSet was created.

```bash
kubectl get pods
```

#### Step 6

With your pod running, it's time to put it behind a service. Use the `kubectl create`command to create the `auth` service.

```bash
kubectl create -f services/auth.yaml
```

#### Step 7

Do the same to create and expose the `hello` and `frontend` deployments.

```bash
kubectl create -f deployments/hello.yaml
kubectl create -f services/hello.yaml
kubectl create configmap nginx-frontend-conf --from-file=nginx/frontend.conf

kubectl create secret generic tls-certs --from-file tls/

kubectl create -f deployments/frontend.yaml
kubectl create -f services/frontend.yaml
```

You created a ConfigMap and secret for the frontend.

#### Step 8

Interact with the frontend.

Get its external IP.

```bash
kubectl get services frontend
```

You may need to re-run this command every few seconds until the External IP is populated.

And `curl` the service.

```bash
curl -ks https://<EXTERNAL-IP>
```

You get the "hello" response. Use the output templating feature of kubectl to run curl as a one-line command.

```bash
curl -ks https://`kubectl get svc frontend -o=jsonpath="{.status.loadBalancer.ingress[0].ip}"`
```

### Scale a Deployment

Update the `spec.replicas` field to scale the deployment.

#### Step 1

Run the `kubectl explain` command to see an explanation of the field.

```bash
kubectl explain deployment.spec.replicas
```

#### Step 2

You can update the `replicas` field most easily using the `kubectl scale`command.

```bash
kubectl scale deployment hello --replicas=5
```

It may take a minute or so for all the new pods to start up.

#### Step 3

Kubernetes updates the ReplicaSet and starts new pods to equal 5.

Verify there are 5 pods running.

```bash
kubectl get pods | grep hello- | wc -l
```

#### Step 4

Scale back the application.

```bash
kubectl scale deployment hello --replicas=3
```

#### Step 5

Verify the correct number of pods.

```bash
kubectl get pods | grep hello- | wc -l
```

**Congratulations!**

You learned about Kubernetes deployments and how to manage and scale a group of pods.

## Rolling Updates

Deployments update images to new versions through rolling updates. When a deployment is updated with a new version, it creates a new ReplicaSet and slowly increases the number of replicas in the new ReplicaSet as it decreases the replicas in the old ReplicaSet.

### Trigger a Rolling Update

#### Step 1

Run the following command to update your deployment.

```bash
kubectl edit deployment hello
```

#### Step 2

Change the `image` in `containers` section to the following, then save and exit.

```
containers:
- name: hello
  image: kelseyhightower/hello:2.0.0
```

The editor uses **vi** commands:

1. Use arrow keys to hover over version number **1**
2. Type **r** to replace it, and enter **2**
3. Type **:wq!** and hit **Enter** to write and quit the file.

If you have difficulty and are in a class, ask your instructor for help.

The updated deployment is saved to your cluster and Kubernetes begins a rolling update.

#### Step 3

You can see the new ReplicaSet that Kubernetes creates.

```bash
kubectl get replicaset
```

If you fail to see a new ReplicaSet, make sure you changed the `image` in `containers`, and not one of the other references in `labels`.

#### Step 4

View the new entry in the rollout history.

```bash
kubectl rollout history deployment/hello
```

### Pause a Rolling Update

If you detect problems with a running rollout, pause it to stop the update.

#### Step 1

Pause the update.

```bash
kubectl rollout pause deployment/hello
```

#### Step 2

Verify the current state of the rollout.

```bash
kubectl rollout status deployment/hello
```

#### Step 3

Verify this with the pods.

```bash
kubectl get pods -o jsonpath --template='{range .items[*]}{.metadata.name}{"\t"}{"\t"}{.spec.containers[0].image}{"\n"}{end}'
```

### Resume a Rolling Update

The rollout is paused which means that some pods are at the new version and some pods are at the older version.

#### Step 1

Use the `resume` command to continue the rollout.

```bash
kubectl rollout resume deployment/hello
```

#### Step 2

Run the status command to verify the rollout is complete.

```bash
kubectl rollout status deployment/hello
```

You'll get the following:

```bash
deployment "hello" successfully rolled out
```

### Rollback an Update

If a bug occurs in your new version, users connected to new pods will experience the issue.

#### Step 1

Use the `rollout undo` command to roll back to the previous version, then fix any bugs.

```bash
kubectl rollout undo deployment/hello
```

#### Step 2

Verify the rollback in the deployment's history.

```bash
kubectl rollout history deployment/hello
```

#### Step 3

Verify all pods have rolled back to the previous version.

```bash
kubectl get pods -o jsonpath --template='{range .items[*]}{.metadata.name}{"\t"}{"\t"}{.spec.containers[0].image}{"\n"}{end}'
```

**Congratulations!**

You learned how to roll out application updates without downtime.

## Canary Deployments

Run a canary deployment to test a new deployment in production with a subset of users. This mitigates risk with new releases.

### Create a Canary Deployment

A canary deployment consists of a separate deployment from your stable deployment and a service that targets them both at the same time.

#### Step 1

Examine the file that creates a canary deployment for your new version.

```bash
cat deployments/hello-canary.yaml
```

It includes the following:

- the deployment `hello-canary`
- 1 pod (replica)
- selectors `app: hello` and `track: canary`
- an image with version 2.0.0.

#### Step 2

Create the canary deployment.

```bash
kubectl create -f deployments/hello-canary.yaml
```

#### Step 3

After the canary deployment is created, verify you have two deployments `hello` and `hello-canary`.

```bash
kubectl get deployments
```

The `hello` service selector uses `app: hello`, which matches pods in both deployments. However, the canary deployment has fewer pods, and is only used by a subset of users.

### Verify the Canary Deployment

You can verify both `hello` versions being served by requests.

```bash
curl -ks https://`kubectl get svc frontend -o=jsonpath="{.status.loadBalancer.ingress[0].ip}"`/version
```

Run the command several times and confirm that `hello 1.0.0` serves about ¾ (75%) of requests and `2.0.0` serves about ¼ (25%).

By default, every request has a chance to be served by the canary deployment. If you want users to get all their responses from the same version, enable session affinity in the configuration file as follows:

```
spec:
sessionAffinity: ClientIP
```

### Clean Up

You're done using the canary deployment.

Delete it and the service as follows.

```bash
kubectl delete deployment hello-canary
```

**Congratulations!**

You learned about canary deployments and how to test new versions of an application in a live environment.

## Blue-Green Deployments

You can use blue-green deployments if it's more beneficial to modify load balancers to point to a new, fully-tested deployment all at once.

A downside is you need double the resources to host both versions of your application during the switch.

### The Service

You use the existing `hello` deployment for the blue version and a new `hello-green`deployment for the green version.

Deployments have the following label:

| Deployment   | Label Name | Label Value |
| ------------ | ---------- | ----------- |
| hello (blue) | version    | 1.0.0       |
| hello-green  | version    | 2.0.0       |

You use two nearly-identical service files (`hello-blue` and `hello-green`) to switch between versions. The only difference between these files is their `version` selector. You could edit the service while it's running and change the `version` selector, but switching files is easier for labs.

First, update the service to use the blue deployment:

```bash
kubectl apply -f services/hello-blue.yaml
```

### Create a Blue-Green Deployment

#### Step 1

Create the green deployment.

```bash
kubectl create -f deployments/hello-green.yaml
```

#### Step 2

Verify the blue deployment (1.0.0) is still being used.

```bash
curl -ks https://`kubectl get svc frontend -o=jsonpath="{.status.loadBalancer.ingress[0].ip}"`/version
```

#### Step 3

Run the following command to update the service to use the green deployment.

```bash
kubectl apply -f services/hello-green.yaml
```

#### Step 4

Verify the green deployment is being used.

```bash
curl -ks https://`kubectl get svc frontend -o=jsonpath="{.status.loadBalancer.ingress[0].ip}"`/version
```

### Rollback a Blue-Green Deployment

You can roll back to the old version.

#### Step 1

While the green deployment is still running, simply update the service to the old (blue) deployment.

```bash
kubectl apply -f services/hello-blue.yaml
```

#### Step 2

Verify that the blue deployment is being used.

```bash
curl -ks https://`kubectl get svc frontend -o=jsonpath="{.status.loadBalancer.ingress[0].ip}"`/version
```

**Congratulations!**

You learned how to use blue-green deployments to switch application versions all at once.

## Use the Web UI

You can also use Admin Console to view and interact with your Kubernetes Engine objects in the web interface rather than the command-line.

#### Step 1

In Admin Console, click **Navigation menu** and select **Kubernetes Engine**.

Select a resource in the tab on the left, for example **Services**.

![image2.png](assets/1d7310e68ddb4d8bfcbbe7a0322897fa2e5ef6a60d8900cc3a697ad90801e97c.png)

#### Step 2

Investigate each set of objects by navigating through the following pages:

- **Clusters**
- **Workloads** (Pods)
- **Services**
- **Configuration** (ConfigMaps and Secrets).

On each page, scroll over an item in the **Name** field and click its link to view more information about it. For example, on the **Services** page, click **frontend**. Your front end Services will display as follows with status, types, endpoints, and number of running Pods:

![image3.png](assets/392343cffa5f546a76ea62654bb5a6f921c59a44439f8d225e95c2212d90ad40.png)