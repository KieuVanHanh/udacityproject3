# Coworking  Space Service Extension
The Coworking Space Service is a set of APIs that enables users to request one-time tokens and administrators to authorize access to a coworking space. This service follows a microservice pattern and the APIs are split into distinct services that can be deployed and managed independently of one another.

## 1: Set Up a Postgres Database with Helm Chart
Pre-requisites:
- Have Kubernetes cluster ready.
- Have `kubectl` installed and configured to interact with your cluster.
- Have Helm installed.

Instructions:
1 Configure a Database for the Service
1.1 Create PersistentVolume
   <table><tbody><tr><td><code>kubectl apply -f pv.yaml</code></td></tr></tbody></table>
1.2 Create PersistentVolumeClaim
   <table><tbody><tr><td><code>kubectl apply -f pv.yaml</code><br></td></tr></tbody></table>
1.3 Create Postgres Deployment
   <table><tbody><tr><td><code>kubectl apply -f postgresql-deployment.yaml</code></td></tr></tbody></table>
2 Verify the database:
   <table><tbody><tr><td><code>helm list</code><br><code>kubectl get pods</code></td></tr></tbody></table>
3. Port Forwarding

*   Use **kubectl port-forward** to forward PostgreSQL service port (5432) to local machine.
*   Connect to the database using **psql** with forwarded port.

<table><tbody><tr><td><code>kubectl port-forward --namespace default svc/my-postgresql2 5432:5432 &amp;</code><br><code>PGPASSWORD="$POSTGRES_PASSWORD" psql --host 127.0.0.1 -U postgres -d postgres -p 5432 &lt; db/1_create_tables.sql</code></td></tr></tbody></table>

4. Seed Database

*   Run SQL seed files located in the **db/** directory to create tables and populate them with data.

<table><tbody><tr><td><code>kubectl port-forward --namespace default svc/my-postgresql2 5432:5432 &amp;</code><br><code>PGPASSWORD="$POSTGRES_PASSWORD" psql --host 127.0.0.1 -U postgres -d postgres -p 5432 &lt; db/2_seed_users.sql></code></td></tr></tbody></table>
<table><tbody><tr><td><code>kubectl port-forward --namespace default svc/my-postgresql2 5432:5432 &amp;</code><br><code>PGPASSWORD="$POSTGRES_PASSWORD" psql --host 127.0.0.1 -U postgres -d postgres -p 5432 &lt; db/2_seed_token.sql></code></td></tr></tbody></table>

*   Testing the validity of the populated data.
  
## 2: Local Testing of Analytics Application

*   Navigate to the **analytics/app** directory.
*   Install requirements for the analystics application.  
    

<table><tbody><tr><td><code>python -m pip install --upgrade pip</code><br><code>pip install -r analytics/app/requirements.txt</code></td></tr></tbody></table>

*   Run the analytics application locally:

<table><tbody><tr><td><code>DB_USERNAME=hanhkv DB_PASSWORD=hanh1998 python analystics/app/app.py</code></td></tr></tbody></table>


*   Test the application using curl commands.

<table><tbody><tr><td><code>curl http://127.0.0.1:5153/api/reports/daily_usage</code><br><code>curl http://127.0.0.1:5153/api/reports/user_visits</code></td></tr></tbody></table>

## 3: Docker Image Creation and Local Deployment

*   Build the Docker image locally:
Access to docker file 
<table><tbody><tr><td><code>cd analystics</code></td></tr></tbody></table>
<table><tbody><tr><td><code>docker build -t coworking:v1 .</code></td></tr></tbody></table>

*   Run the Docker container locally.  
    

<table><tbody><tr><td><code>docker run -e DB_USERNAME=hanhkv -e DB_PASSWORD=hanh1998 --network=host coworking:v1</code></td></tr></tbody></table>


## 4: AWS CodeBuild Pipeline

*   Configure CodeBuild to pull the image from the GitHub repository, build it, and push it to the existing ECR repository. Make sure to use the buildspecs.yml file.


## 5: Kubernetes Deployment

*   Deploy the application using the **[Deployment](analystics/analystics-deployment.yaml)** file, **[configMap](analystics/configMap.yml)** file, and **[Secret](analystics/secret.yml)** file.
*   Set **DB\_HOST** to the service name of the Kubernetes PostgreSQL pod.


##6: CloudWatch Agent Setup

*   Set up CloudWatch agent for monitoring.

Saving costs:
Implement Autoscaling:
Utilize Kubernetes Horizontal Pod Autoscaling (HPA) to automatically adjust the number of replicas based on resource metrics. This helps scale resources up during peak demand and down during low demand, optimizing costs.

Rightsize Resources:
Regularly review and adjust resource allocations for your Kubernetes pods based on actual usage. Avoid overprovisioning, and set resource limits and requests appropriately.

Alerts on Costly Resources:
Set up alerts based on cost thresholds to be notified when spending exceeds predefined limits. This allows for proactive cost management.

Remember to monitor your application's resource usage and adjust your instance types based on evolving requirements.
