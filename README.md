Kubernetes Settings
===================
I will be pushing infrastructure-as-a-code for enabling my [Microservice POC application](https://github.com/pablo-iglesias/microservice) with Kubernetes based enviornments.

### Settings
Find description below

| Folder               | Environment              | Nodes  |  Database          |
| -------------------- |:------------------------ | ------:|  -----------------:|
| [katacoda](katacoda) | Katacoda Playground      |      1 |  SQLite in memory  |
| [gke](gke)           | Google Kubernetes Engine |      3 |  MySQL             |

### Watch for the Dockerfile in this folder
It will generate a Docker image with an environment ready to compile and launch the microservice.
The resulting image has been pushed to docker hub with the tag **pabeagle/pablo:latest**.
All the settings use this image to deploy the containerized app.

It does the following things sequentially
1. Install Java & JDK
2. Install GIT
3. Clone microservice repo
4. Run gradle rapper in order to download dependencies
5. Create a shell script in the base path intended to be called upon container startup

The script, called **init.sh** will do the following
* Pull latest changes from GIT repo
* Build the application
* If built succesfully, run the application