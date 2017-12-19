GKE Kubernetes Setting
----------------------------------------

Find in this folder infrastructure-as-a-code for setting up the application in the Google Kubernetes Engine. This is a distributed setting that uses 3 nodes and 1 pod per node and MySQL as its database.

File     | Description
-------- | ---
secret.yaml    | Defines a secret, with a number of settings base64-encoded. The settings include debug mode enabled, select MySQL as the data source and inject host, port, user and password for connecting to the database
deployment.yaml     | Defines the deployment, specifying the number of pods. Also defines application container based in microservice image, tells kubectl to run the shell script in the base path and injects the previously defined secrets as enviornment vars in the containers, exposing the port 8000 to the environment. Another container is setup en each pod that will host a proxy for connecting to MySQL instance
service.yaml | Provisioned load balancer that maps the port 8000 of the containers to external port 80, enables sticky sessions in order to avoid loss of session due to distributed enviornment

What do we need
----------------
We need a **Google Cloud Services** account. As of today, the free tier covers all the activities hereunder.

How to proceed
----------------
(These points are developed below)
1. Create a Google Cloud Project
2. Create a Kubernetes Cluster
3. Create a Cloud SQL instance
4. Create Cloud SQL Service Account
5. Create the Proxy user
6. Create Cloud SQL secret
7. Setup Kubernetes Deployment

Create a Google Cloud Project
----------------
Just create a new project from your GCP console ([watch documentation](https://cloud.google.com/resource-manager/docs/creating-managing-projects))

Create a Kubernetes Cluster
----------------
From the very console ([click here](https://console.cloud.google.com/kubernetes/list)), create a cluster

You need to specify:
* Region, whatever closest to you
* Number of nodes, 3
* Machine type, at least **g1-small**

Wait for the cluster to create. Meanwhile you can open a Cloud Shell.
When the cluster is up and running, click the button *Connect*, copy the gcloud container command, paste it in the shell and run it. After this you should be able to monitor the state of your nodes with **kubectl get nodes** with status *Ready* if nothing went wrong.

Create a Cloud SQL instance
----------------
Go create yourself a [Google Cloud SQL Instance](https://cloud.google.com/sql/docs/mysql/create-instance)

You need to specify:
* Type, MySQL Second Generation
* Instance name, **webapp1-db**
* Password, **MAya4PJyWSDXab6u** (use this or it will not work)
* Region, the same as the cluster
* Take note of your **instance connection name**, you will need it later

When you are done with that, select your MySQL instance and create a database
* Name, **poc** (use this or it will not work)
* Charset, utf8
* Collation, utf8-unicode-ci

This is not over

Create Cloud SQL Service Account
----------------
First you should [Enable Clould SQL Administration API](https://console.cloud.google.com/flows/enableapi?apiid=sqladmin&redirect=https://console.cloud.google.com). After that, go to the [Cloud SQL Service accounts page](https://console.cloud.google.com/iam-admin/serviceaccounts/), select your project and click **Create service account**.

You need to specify:
* Role, select Cloud SQL > Cloud SQL Client.

Click *Furnish a new private key* and download a JSON file. Lets call it **service account key file**. You will need it later.

Create the Proxy user
----------------
Use the gcloud command below to create the user account named **proxyuser**, that the proxy will use to access your Cloud SQL instance.

> gcloud sql users create proxyuser cloudsqlproxy~% --instance=webapp1-db --password=MAya4PJyWSDXab6u

Create Cloud SQL secret
----------------
Create a file in your Cloud Shell with the name **credentials.json**, then take the **service account key file** and copy its contents to the newly created file. Then execute the following command.

> kubectl create secret generic cloudsql-instance-credentials --from-file=credentials.json=<PATH_TO_THE_NEWLY_CREATED_FILE>

Setup Kubernetes Deployment
----------------
Run the following commands in your Cloud Shell
> git clone https://github.com/pablo-iglesias/kubernetes-settings.git<br/>
cd kubernetes-settings/gke/<br/>

Edit the file **deployment.yaml** set the wildcard <INSTANCE_CONNECTION_NAME> to your **instance connection name**

> kubectl create -f secret.yaml<br/>
kubectl create -f deployment.yaml<br/>
kubectl create -f service.yaml<br/>
kubectl get pods

Check that the pods are all running

> kubectl get service webapp1

Copy the external IP (wait if pending) and paste it in your browser, you should see the application running.
You can login with admin|admin or user1|pass1.

Because debug mode is turned, you can grab the id of a POD and watch the stdout with the following command

> kubectl logs <POD_ID> webapp1

This is helpful to see what node is serving each client and check that sticky sessions work