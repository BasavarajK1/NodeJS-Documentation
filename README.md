# NodeJS Project
## _Contents_
- Introduction.
- Software tools.
- Project Overview.

### Introduction.
In this project we used NodeJs web application to automate the infrastructure set up on AWS Cloud using Terraform. We created a VPC with subnets, route tables, internet gateway, NAT gateway, etc... We also created EKS Cluster using the same terraform. Later deployed the application in different environments like DEV, QA and PROD using Jenkins pipeline and then pushed the image to ECR. We monitored the application using the monitoring tools like Prometheus and Grafana, further we checked the logs of the application using logging tools like ElasticSearch, Fluent-bit and Kibana. For details of the source code please visit https://github.com/namrata-aatmani/productionteam.git.

### Software Tools.

- Amazon web service (AWS).
- Github.
- Terraform.
- Jenkins.
- Docker.
- Kubernetes.
- Helm.
- Elasticsearch.
- Fluent-bit.
- Kibana.
- Prometheus.
- Grafana.

#### Description.
- Amazon Web service (AWS):- AWS (Amazon Web Services) is a comprehensive, evolving cloud computing platform provided by Amazon that includes a mixture of infrastructure as a service (IaaS), platform as a service (PaaS) and packaged software as a service (SaaS) offerings. AWS services can offer an organization tools such as compute power, database storage and content delivery services.
- Github:- GitHub is a code hosting platform for version control and collaboration. It lets you and others work together on projects from anywhere. GitHub contains essentials like repositories, branches, commits, and pull requests.
- Terraform:- Terraform is an infrastructure as code tool that lets you define both cloud and on-premises resources in human-readable configuration files that you can version, reuse, and share.
- Jenkins:- Jenkins is an open source automation server. It helps automate the parts of software development related to building, testing, and deploying, facilitating continuous integration and continuous delivery.
- Docker:- Docker is a software platform that simplifies the process of building, running, managing and distributing applications. It does this by virtualizing the operating system of the computer on which it is installed and running.
- Kubernetes:- Kubernetes, often abbreviated as “K8s”, orchestrates containerized applications to run on a cluster of hosts. The K8s system automates the deployment and management of cloud native applications using on-premises infrastructure or public cloud platforms.
- Helm:- Helm is a package manager for Kubernetes. It deploys charts, which you can think of as a packaged application. It is a collection of all your versioned, pre-configured application resources which can be deployed as one unit.
- Elasticsearch:- Elasticsearch allows you to store, search, and analyze huge volumes of data quickly and in near real-time and give back answers in milliseconds. It's able to achieve fast search responses because instead of searching the text directly, it searches an index.
- Fluent-bit:- Fluent Bit is a super fast, lightweight, and highly scalable logging and metrics processor and forwarder. Fluentd was designed to handle heavy throughput — aggregating from multiple inputs, processing data and routing to different outputs.
- Kibana:- Kibana is a data visualization and exploration tool used for log and time-series analytics, application monitoring, and operational intelligence use cases. It offers powerful and easy-to-use features such as histograms, line graphs, pie charts, heat maps, and built-in geospatial support.
- Prometheus:- Prometheus is an open-source system monitoring and alerting toolkit originally built at SoundCloud. Prometheus is a time-series database that scrapes the metrics from the HTTP/HTTPS endpoint. Prometheus provides native support to some services which means that they don’t require any kind of monitoring agent, for example:- Kubernetes, ETCD.
- Grafana:- Grafana is a multi-platform open source analytics and interactive visualization web application. It provides charts, graphs, and alerts for the web when connected to supported data sources.

### Project Overview.
![Project](https://github.com/BasavarajK1/NodeJS-Documentation/blob/main/infrastructure-new.png)

#### Step 1
##### Creation of VPC and EKS Cluster using Terraform and GitHub Configuration.
Initially, we created an EC2 Instance in AWS console and install Terraform on it. Using terraform code we installed VPC (Virtual Private Cloud), Subnets, Route tables, Internet gateway, NAT gateway and Kubernetes Cluster with one nodegroup which contains minimum 1 spot machine and maximum 5.

#### Step 2
##### Creating an organization in GitHub.
We created an Organisation in GitHub and made two repositories which are named as Production-team and development. 
 - In Production-team repo we pulled the code of the NodeJS Project, where all the source code is stored. 
 - Added the Dockerfile  written according to the requirements. Also, added GIT_COMMIT to get the latest image from the ECR.
 - In development repo we pushed the Helm chart resources created for NodeJS contains values depending on environments dev-values.yml, qa-values.yml, prod-values.yml and pushed the Jenkins pipeline scripts accordingly.
 
#### Step 3
##### Git merge and branch protection rules.
In github organization namely namrata-aatmani's productionteam repo, if a developer updates any code change in the main branch, it should not directly merge to the main branch, instead it should raise a Pull Request (PR) using the branch protection rules and one of the team member should review, confirm and approve the changes. Thereafter the code is merged in the main branch after approval.

#### Step 4
##### Installation of AWSCLI ,Docker, Kubernetes and Helm.
In this step we installed AWS CLI, Docker, Kubernetes and Helm in the EC2 machine.
- Created 3 namespaces which represent the environments for the workflow. These are dev, qa and prod environments in the EC2 machine where all the installations are done.
- Next create the Helm chart and create 3 different values.yaml files for each environments seperately, i.e. dev-values.yaml, qa-values.yaml, prod-values.yaml.
- Change the template charts according to the requirements (if needed).

#### Step 5
##### Amazon ECR Repo.
Create an Amazon ECR repository and push the latest image.

#### Step 6
##### Installation of Jenkins,Slack integration and pipeline scripts for dev environment, qa environment and prod environment.
- Install Java, Jenkins in the EC2 machine and install all the required AWS, Slack plugins in Jenkins.
- Create credentials for Slack and AWS (secret text).
- Create 3 pipeline scripts as dev, qa, prod and push it to git repo (development).
###### Stage-1:- Build and Deploy to the Dev.
In this stage we created pipeline scipt in development repo. As we run the script, it will pull the source code and run docker build command to create an image with the GIT_COMMIT id and upload it to the AWS ECR. We configured the pipeline to execute whenever there is a commit made in the git repo.
###### Stage-2:- Deploy to the QA.
In this stage we created similar pipeline script like dev-pipeline. But the only changes made here is that it will pull the latest image uploaded in ECR by dev and make the required tag changes and upload it to ECR, which in turn will be deployed in the qa environment. Here we configured Slack integration alerts to know the job is success/failed.
###### Stage-3:-Deploy to the Prod.
Here we used the same pipeline script like qa-pipeline. The job will pull the latest image uploaded by qa environment from ECR and make te required tag changes and upload it to ECR, which in turn will be deployed in prod environment. We configured Slack alerts to know the job is success/failed.
#### Step 7
##### Deploying Metric server,Cluster Autoscalar and HPA (Horizontal Pod Autoscalar).
- Metric server:- We will be able to see cpu and memory utilization metrics with metrics-server.The Metrics server role frequently checks the metrics of every running pods in EKS cluster. The main role of this server is it will help to Horizontal Pod Autoscaler and Vertical Pod Autoscaler to increase or decrease accordingly. Metrics Server is a scalable, efficient source of container resource metrics for Kubernetes built-in autoscaling pipelines. Metrics Server collects resource metrics from Kubelets and exposes them in Kubernetes apiserver through Metrics API for use by Pod autoscaler (HPA ad VPA).
- Cluser Autoscaler:- The Kubernetes Cluster Autoscaler automatically adjusts the number of nodes in your cluster when pods fail or are rescheduled onto other nodes. The Cluster Autoscaler is typically installed as a Deployment in your cluster. It uses leader election to ensure high availability, but scaling is done by only one replica at a time.
- HPA (Horizontal Pod Autoscaler):- The Kubernetes Horizontal Pod Autoscaler automatically scales the number of pods in a deployment, replication controller, or replica set based on that resource's CPU utilization. This can help your applications scale out to meet increased demand or scale in when resources are not needed, thus freeing up your nodes for other applications. When you set a target CPU utilization percentage, the Horizontal Pod Autoscaler scales your application in or out to try to meet that target.

#### Step 8
##### Installation and Configuration of Monitoring Tools Prometheus and Grafana to EKS Cluster.

![Monitoring](https://github.com/BasavarajK1/NodeJS-Documentation/blob/main/monitoring.png)

1. Prometheus:- 

Prometheus is a free software application used for event monitoring and alerting thus Prometheus is an open-source systems monitoring and alerting toolkit. A typical monitoring platform with Prometheus is composed of multiple tools:

- Prometheus server: The main Prometheus server which scrapes and stores time series data
- Client libraries: Client libraries lets you define and expose internal metrics via an HTTP endpoint on your application’s instance. Prometheus Client libraries support multiple programming languages
- Push gateway: Occasionally you will need to monitor components which cannot be scraped. The Prometheus Pushgateway allows you to push time series from short-lived service-level batch jobs to an intermediary job which Prometheus can scrape.
- Node Exporters: Special-purpose exporters for services like HAProxy, StatsD, Graphite, etc.
- Alertmanager: An alertmanager to handle alerts.

Prometheus working procedure:-
- Prometheus collects data in the form of time series. The time series are built through a pull model:
- The Prometheus server queries(scrape) a list of data sources (sometimes called exporters) at a specific polling frequency
- Prometheus data is stored in the form of metrics, with each metric having a name that is used for referencing and querying it
- Prometheus stores data locally on disk, which helps for fast data storage and fast querying but ability to store metrics in remote storage.
- Each Prometheus server is standalone, not depending on network storage or other remote services.

2. Grafana:- 

Grafana is an open-source observability platform for visualizing metrics, logs, and traces collected from your applications. It’s a cloud-native solution for quickly assembling data dashboards that let you inspect and analyze your stack.
Grafana connects to a variety of data sources such as Prometheus, InfluxDB, ElasticSearch, and traditional relational database engines. Complex dashboards are created by using these sources to select relevant fields from your data. Dashboards can incorporate a varied range of visualization components such as graphs, heat maps, and histograms.

Later, we set some alerts for Kubernetes cluster such as:- 
1. Pod is pending state.
2. Pod is CrashLoopoff.
3. Node is down.
4. Hard disk space is low.

When the target is unavilable then alert manager send an alert to the slack channel.

#### Step 9
##### Logging Tools installation and setup of EFK (Elasticsearch, Fluent-bit, Kibana).

![Logging](https://github.com/BasavarajK1/NodeJS-Documentation/blob/main/logging.png)

1. Elasticsearch.
Elasticsearch is a distributed, open-source search and analytics engine built on Apache Lucene and developed in Java. Elasticsearch allows you to store, search, and analyze huge volumes of data quickly and in near real-time and give back answers in milliseconds. It’s able to achieve fast search responses because instead of searching the text directly, it searches an index. It uses a structure based on documents instead of tables and schemas and comes with extensive REST APIs for storing and searching the data.
2. Fluent-bit.
Fluent bit is an open source, light-weight, and multi-platform service created for data collection mainly logs and streams of data. Fluent bit service can be used for collecting CPU metrics for servers, aggregating logs for applications/services, data collection from IOT devices(like sensors) etc.
3. Kibana.
Kibana is an free and open frontend application that sits on top of the Elastic Stack, providing search and data visualization capabilities for data indexed in Elasticsearch. Commonly known as the charting tool for the Elastic Stack. Kibana also acts as the user interface for monitoring, managing, and securing an Elastic Stack cluster — as well as the centralized hub for built-in solutions developed on the Elastic Stack. 

Installation and setup:-
Initially we created 1 seperate EC2 machine for log monitoring, where ELasticsearch and Kibana are installed. Elasticsearch store logs and retrive logs from fluent-bit. Then Kibana is installed which is a UI tool for querying, data visualization and dashboards. It is a query engine which explores log data through a web interface, build visualizations for events log, query-specific to filter information for detecting issues. 

Next, we deployed Fluent-bit in the cluster as a DaemonSet because, Fluent-bit is a log agent tool, it has to run on every node to collect logs from all the pods. When Fluent-bit runs, it will read, parse and filter the logs of all pods and it transforms and ships to Elasticsearch backend. In Elasticsearch data are analyzed and it moves the data/logs to Kibana and there the visualization of data takes place.
