Katacoda Kubernetes Setting
----------------------------------------
File     | Description
-------- | ---
Dockerfile | Generates a Docker image with an environment ready to compile and launch the microservice. Adds, Java JDK, GIT, clones microservice repo, runs graddle wrapper in order to download dependencies, creates a shell script in the base path that will pull latest changes from GIT repo, build and run the application. The resulting image has been pushed to docker hub with the tag **pabeagle/pablo:latest**
secret.yaml    | Defines a secret, with the database engine that the microservice shall use, which is **SQLITE_MEMORY**, encoded with base64
deployment.yaml     | Defines the deployment, specifying what is the image that shall be used, and telling kubectl to run the shell script in the base path. Also injects the previously defined secret as an enviornment var in the container. Exposes the port 8000 to the environment.
service.yaml | Maps the port 8000 of the containers to an external port at 30080

How to run
----------------
- Go to the [Katacoda Kubernetes Playground](https://www.katacoda.com/courses/kubernetes/playground)
- Launch a cluster
- Set the focus to Terminal Host 1
- git clone https://github.com/pablo-iglesias/Kubernetes-settings.git
- cd Kubernetes-settings/katacoda/
- kubectl create -f secret.yaml
- kubectl create -f deployment.yaml
- kubectl create -f service.yaml
- Click the + sign in the header of the terminal
- Select port to view on Host 1
- In the Web Preview Port Selector specify 30080 and click Display Port
- You can login with admin|admin or user1|pass1