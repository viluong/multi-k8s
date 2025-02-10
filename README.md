# multi-k8s
Build a microservie system using kubernetes with Google Cloud service.
- ReactJS for frontend in **client** folder.
- FastApi application in **server** folder.
- Pubsub messages system is written by Python in **worker** folder.
- Kubernetes configuration files in **k8s** folder.
- Use Github Actions to build CI/CD in [this file](https://github.com/viluong/multi-k8s/blob/master/.github/workflows/deploy.yaml)
  
### MY DIAGRAM:

![diagam drawio](https://github.com/user-attachments/assets/9746ffb5-63a4-4fa6-8733-9213ac14b53f)

### FREE GOOGLE CLOUD CREDITS

Creating Kubernetes clusters on Google Cloud costs real money! If you are sensitive to spending money, you can try getting some free Google Cloud credits using this link:
https://cloud.google.com/free

### CREATE NEW PROJECT IN GOOGLE CLOUD

1. Click to the button project on the top left-hand side of the dashboard

![image](https://github.com/user-attachments/assets/929c43d5-f7d9-4b61-932e-b4250544aa28)

2. Click New project and inputing **Project name** is ```multi-k8s```. > **Create**

### KUBERNETES ENGINE INIT

1. Click the Hamburger menu on the top left-hand side of the dashboard.

![image](https://github.com/user-attachments/assets/1f53e518-7b05-417d-a701-913e2ae5a1ab)

2. Click **Kubernetes Engine** and **ENABLE** button to enable the Kubernetes API for this project.

3. After a few minutes of waiting, clicking the **bell** icon in the top right part of the menu should show a **green** checkmark for **Enable services: container.googleapis.com**

4. If you refresh the page it should show a screen to create your first cluster. If not, click the hamburger menu and select **Kubernetes Engine** and then **Clusters**. After that, click the **CREATE** button. 

![image](https://github.com/user-attachments/assets/5215fb63-d864-4bfb-a2fc-f88ba6204458)

5. A Create Cluster dialog will open and default to Autopilot. You must make sure to click the **SWITCH TO STANDARD CLUSTER** option. Autopilot is not supported by this demo and is a very different tool.

6. A form for creating cluster will be show, Set the **Name** to **multi-cluster** (step 1). Confirm that the **Zone** set is actually near your location (step 2). The Node Pool that is now found in Node is in a separate dropdown on the left sidebar. Click the downward-facing arrow to view the settings. No changes are needed here (step 3). Finally, click the **CREATE** button at the bottom of the form (step 4).

7. After a few minutes, the cluster dashboard should load and your multi-cluster should have a green checkmark in the table.

### CREATE KUBERNETES SECRETS IN GOOGLE CLOUD

1. Go to **Active Cloud Shell** feature in **Google Cloud Console** and connect to project.

![image](https://github.com/user-attachments/assets/6f716474-6d5e-40d1-b92b-c38852a1cb3c)


2. Run cmd below to create secrets variable in google cloud

```
kubectl create secret generic env \
  --from-literal=PG_USER=your_pg_user \
  --from-literal=PG_HOST=postgres-cluster-ip-service \
  --from-literal=PG_DATABASE=your_pg_database \
  --from-literal=PG_PASSWORD=your_pg_password \
  --from-literal=PG_PORT=5432 \
  --from-literal=REDIS_HOST=redis-cluster-ip-service \
  --from-literal=REDIS_PORT=6379
```
  **Notice:** You must keep these values ```PG_HOST=postgres-cluster-ip-service``` and ```REDIS_HOST=redis-cluster-ip-service```. Because ```postgres-cluster-ip-service``` and ```redis-cluster-ip-service``` represent for cluster ip services of postgres deployment and redis deployment.

### INSTALL INGRESS_NGINX WITH HELM

1. Go to **Active Cloud shell** on **Google Cloud Console**.

![image](https://github.com/user-attachments/assets/33dd6995-2116-42f2-b11d-bda7700d4388)

 
3. Install **Helm** from script in [here](https://helm.sh/docs/intro/install/).

4. Install **Ingress-Nginx** with **Helm** in [here] (https://kubernetes.github.io/ingress-nginx/deploy/#quick-start)


### CREATE KEY FOR PERMISSION USING KUBERNETES ENGINE

1. Click the Hamburger menu on the top left-hand side of the dashboard, find **IAM & Admin**, and select Service Accounts. Then click the **CREATE SERVICE ACCOUNT** button.

![image](https://github.com/user-attachments/assets/315c0cde-a9f0-4ead-b36c-3f7510fc3a28)

2. In the form that is displayed, set the **Service account name** to **github-actions-deployer** (step 1), then click the **CREATE** button (step 2).

3. Click in the **Select a role** filter and scroll down to select **Kubernetes Engine** and then **Kubernetes Engine Admin**.

4. Make sure the filter now shows Kubernetes Engine Admin and then click CONTINUE.

5. The Grant users access form is optional and should be skipped. Click the DONE button.

6. You should now see a table listing all of the service accounts including the one that was just created. Click the three dots to the right of the service account you just created. Then select **Manage Keys** in the dropdown.

![image](https://github.com/user-attachments/assets/b6b4c2c2-7b62-45db-9670-f8da116af0b3)

7. In the Keys dashboard, click ADD KEY and then select Create new key.

8. In the Create private key dialog box, make sure Key type is set to JSON, and then click the CREATE button.

9.  The JSON key file should now download to your computer.

### CREATE REPOSITORY SECRETS VARIABLE IN GITHUB REPOSITORY SETTING

1. Set **DB_HOST_TEST, DB_PORT_TEST, DB_PW_TEST, DB_TEST, DB_USER_TEST, REDIS_HOST_TEST, REDIS_PORT_TEST** for testing postgres database when we run unit test in **Github Actions**.

2. Set **GKE_SA_KEY** value from json file which is downloaded in **CREATE KEY FOR PERMISSION USING KUBERNETES ENGINE** step.

3. Set **GC_PROJECT_ID, GC_CLUSTER_NAME, GC_CLUSTER_LOCATION**. These variables get from google cloud.

4. Set **DOCKER_HUB_TOKEN, DOCKER_HUB_USERNAME**. These variables get from https://hub.docker.com/

### DEPLOYMENT

1. Make a small change to your src/App.js file in the greeting text.

    In the project root, in your terminal run:
    
    git add.
    git commit -m “testing deployment"
    git push origin main
    Go to your Github Actions and check the status of your build.
    
    The status should eventually return with a green checkmark and show "build passing"

2. In Google Cloud Console, Go to **Kubernetes Engine > Gateways, Services, Ingress > SERVICES**

3. As you can see in below:

![image](https://github.com/user-attachments/assets/99c339dc-100e-446f-a3da-0316ee4115e5)

4. Go to http://34.55.128.190:80/ checking your website.
