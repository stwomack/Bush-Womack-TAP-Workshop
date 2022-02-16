# Bush Womack TAP App Workshop
Bush Womack TAP App Workshop
# Deploying .NET and Spring applications to Kubernetes via Tanzu Application Platform”

This is a .NET web site that is intended to be used in conjunction with the [spring-sensors](https://github.com/sample-accelerators/spring-sensors) Application Accelerator. These two applications are intended to be deployed together in a Kubernetes cluster running  [Tanzu Application Platform](https://tanzu.vmware.com/application-platform) (TAP). This .NET application can be used to publish random sensor data to RabbitMQ, and the Spring application can consume that data and display it.

These applications can be used together to demonstrate TAP's ability to easily build and deploy both .NET and Spring workloads, and it shows them sharing data via RabbitMQ. RabbitMQ is hosted in Kubernetes using the [RabbitMQ Cluster Kubernetes Operator](https://github.com/rabbitmq/cluster-operator). .NET Sensors utilizes TAP's [Services Toolkit](https://docs.vmware.com/en/Tanzu-Application-Platform/1.0/tap/GUID-services-toolkit-about.html) to connect to RabbitMQ.

.NET Sensors is provided as an [application accelerator](https://docs.vmware.com/en/Application-Accelerator-for-VMware-Tanzu/index.html) template for TAP.

This has been tested with .NET 6 and TAP 1.0. These instructions assume you have [kubectl installed](https://kubernetes.io/docs/tasks/tools/) as well as the [Tanzu CLI](https://docs.vmware.com/en/Tanzu-Application-Platform/1.0/tap/GUID-install-general.html) that comes with TAP. Note that all CLI commands have only been tested on macOS.

## Before Starting

* Provision a Kubernetes cluster
* [Install TAP](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/index.html) into the cluster using the "full" profile
* Install RabbitMQ
    * Install [RabbitMQ cluster operator](https://github.com/rabbitmq/cluster-operator)
        * `kapp -y deploy --app rmq-operator --file https://github.com/rabbitmq/cluster-operator/releases/download/v1.9.0/cluster-operator.yml `

    * Assign permissions
        * `kubectl apply -f https://raw.githubusercontent.com/fjb4/dotnet-sensors/master/k8s/rabbitmqcluster-read-write.yaml`

    * Create the "example-rabbitmq-cluster-1" RabbitMQ cluster that will be used by the .NET and Spring Sensors applications (wait for it to complete)
        * `kubectl apply -f https://raw.githubusercontent.com/fjb4/dotnet-sensors/master/k8s/rabbitmq-cluster.yaml`
    * Lastly, install the [RabbitMQ Cluster Operator Plugin for kubectl](https://www.rabbitmq.com/kubernetes/operator/kubectl-plugin.html) so you're able to monitor activity in RabbitMQ
* Register the .NET Sensor application accelerator with TAP
    * `tanzu accelerator create dotnet-sensors-rabbit --git-repository https://github.com/fjb4/dotnet-sensors --git-branch main`
* Create an empty GitHub repository for your .NET Sensor application and note its URL.

## Demo Script

### Create and deploy a .NET application that emits data for a single sensor

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
  * `tanzu apps workload create -f ./config/workload.yaml`
* Monitor workload status and get its URL once app has been deployed and is running
  * `tanzu apps workload tail dotnet-sensors`
  * `tanzu apps workload get dotnet-sensors`
* View running application in web browser and click "Start" at the top of the page
  * Note that it shows the sensor id of the sensor for which it is emitting data
* View sensor data being written to the RabbitMQ cluster
  * View the sensor data by watching the application logs
    * `tanzu apps workload tail dotnet-sensors-rabbit`
  * Or watch the activity in the RabbitMQ management GUI
    * Get RabbitMQ username and password
      * `kubectl rabbitmq secrets example-rabbitmq-cluster-1`
    * Open RabbitMQ management GUI and login
      * `kubectl rabbitmq manage example-rabbitmq-cluster-1`
    * View queue activity by going to "Queues" and selecting the queue with a name starting with "sensor-data"

### Create and deploy the SpringBoot application that gets readings from the queue and displays them

* Open your TAP web Interface, for example:  [http://tap-gui.tap.jbush.io/create](http://tap-gui.tap.jbush.io/create)
* Find the "Spring Sensors" application, and click the **"CHOOSE"** button
   * Optional, click **"VIEW REPOSITORY"** to review the template's repository
* On the **"App Accelerator inputs"** page, change the **Name** to "sensors-demo". The rest of the fields can remain their defaults
   * Optional, click **"EXPLORE"** to review the template's project and structure
* Click **"NEXT STEP"** to proceed
* Review/verfiy the "App Accelerator inputs" and click **"CREATE"**
   * Optional, click **"EXPLORE ZIP FILE"** to review the template's project and structure
* Click **"DOWNLOAD ZIP FILE"** to proceed. The zip file will be saved to your default download directory (often ~/Downloads)
* Unzip the downloaded sensors-demo.zip file and move to the newly created sensors-demo folder
  * `unzip ~/Downloads/sensors-demo`
  * `cd sensors-demo`
* Initialize a local git repository, commit your code, and push to GitHub
   * `git init`
   * `git add -A .`
   * `git commit -m "initial commit"`
   * `git remote add origin git@github.com:<{your-org}/sensors-demo>.git`
   * `git branch -M main`
   * `git push -u origin main`
* Deploy the application via TAP (substitute your actual repo url in when running the following command):
   * ```tanzu app workload create sensors-demo --git-repo https://github.com/{your-org}/sensors-demo --git-branch main --type web --service-ref "rmq=rabbitmq.com/v1beta1:RabbitmqCluster:default:example-rabbitmq-cluster-1"```
* In a terminal window, tail the application as TAP builds and deploys it:
   * `tanzu apps workload tail sensors-demo`
* Verify that the application is running by visiting its url, for example http://sensors-demo.default.tap.jbush.io/

### Update the .NET application to emit data for multiple sensors

* In your web browser, go to your GitHub repository and view the file `./src/SensorHostedService.cs`
* Update the application to emit data for two sensors by editing the file and changing line 15 to the following:
  * `AddSensor(2)`
* Commit the change and wait for TAP to redeploy the application
* Refresh the running application again in the web brower and notice that it now shows two sensor ids
  * At the top of the page, click "Start" to have this new version of the application begin emitting sensor data
* Using the same commands as above, view the sensor data being written to the queue

### Update the SpringBoot application to display rounded values for the temperature and pressure sensor data

* TODO
