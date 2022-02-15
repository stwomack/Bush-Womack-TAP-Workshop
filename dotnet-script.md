# .NET Sensors

This is a .NET web site that is intended to be used in conjunction with the [spring-sensors](https://github.com/sample-accelerators/spring-sensors) Application Accelerator. These two applications are intended to be deployed together in a Kubernetes cluster running  [Tanzu Application Platform](https://tanzu.vmware.com/application-platform) (TAP). This .NET application can be used to publish random sensor data to RabbitMQ, and the Spring application can consume that data and display it.

These applications can be used together to demonstrate TAP's ability to easily build and deploy both .NET and Spring workloads, and it shows them sharing data via RabbitMQ. RabbitMQ is hosted in Kubernetes using the [RabbitMQ Cluster Kubernetes Operator](https://github.com/rabbitmq/cluster-operator). .NET Sensors utilizes TAP's [Services Toolkit](https://docs.vmware.com/en/Tanzu-Application-Platform/1.0/tap/GUID-services-toolkit-about.html) to connect to RabbitMQ.

.NET Sensors is provided as an [application accelerator](https://docs.vmware.com/en/Application-Accelerator-for-VMware-Tanzu/index.html) template for TAP.

This has been tested with .NET 6 and TAP 1.0.

## Before Starting

* Provision a Kubernetes cluster
* [Install TAP](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/index.html) into the cluster using the "full" profile
* Install RabbitMQ
    * Install [RabbitMQ cluster operator](https://github.com/rabbitmq/cluster-operator)
        * `kapp deploy --app rmq-operator --file https://github.com/rabbitmq/cluster-operator/releases/download/v1.11.1/cluster-operator.yml `

    * Assign permissions
        * `kubectl apply -f https://raw.githubusercontent.com/fjb4/dotnet-sensors/master/k8s/rabbitmqcluster-read-write.yaml`

    * Create the "example-rabbitmq-cluster-1" RabbitMQ cluster that will be used by the .NET and Spring Sensors applications (wait for it to complete)
        * `kubectl apply -f https://raw.githubusercontent.com/fjb4/dotnet-sensors/master/k8s/rabbitmq-cluster.yaml`
    * Lastly, install the [RabbitMQ Cluster Operator Plugin for kubectl](https://www.rabbitmq.com/kubernetes/operator/kubectl-plugin.html) so you're able to monitor activity in RabbitMQ
* Register the .NET Sensor application accelerator with TAP
    * `tanzu accelerator create dotnet-sensors-rabbit --git-repository https://github.com/fjb4/dotnet-sensors --git-branch main`
* Create an empty GitHub repository for your .NET Sensor application and note its URL.

## Demo Script

### Initial app deployment with support for one sensor

* Generate application skeleton using .NET Sensors app accelerator
  * Navigate to Application Accelerator section of TAP GUI and choose the ".NET Sensors" template
  * Specify the Git Repository URL and Branch for your code (using the GitHub repo creating in the "Before Starting" section)
  * Generate the accelerator and download the zip file
* Unzip the downloaded dotnet-sensors-rabbit.zip file and move to the newly created dotnet-sensors-rabbit folder
  * `unzip ~/Downloads/dotnet-sensors-rabbit`
  * `cd dotnet-sensors-rabbit`
* Initialize a local git repository, commit your code, and push to GitHub
  * `git init`
  * `git add -A .`
  * `git commit -m "initial commit"`
  * `git remote add origin git@github.com:<GITHUB-REPO-URL>.git`
  * `git branch -M main`
  * `git push -u origin main`
* Create a TAP workload
  * `kubectl create -f ./config/workload.yaml`
* Monitor workload status and get URL once app has been deployed and is running
  * `tanzu apps workload tail dotnet-sensors`
  * `tanzu apps workload get dotnet-sensors`
* View running application in web browser
* View sensor data being written to the RabbitMQ cluster
  * Get RabbitMQ username and password
    * `kubectl rabbitmq secrets example-rabbitmq-cluster-1`
  * Open RabbitMQ management GUI and login
    * `kubectl rabbitmq manage example-rabbitmq-cluster-1`
  * View queue activity by going to "Queues" and selecting the queue with a name starting with "sensor-data"

### Update the application to support multiple sensors

* TODO
