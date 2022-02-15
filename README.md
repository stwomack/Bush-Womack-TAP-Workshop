# Bush Womack TAP App Workshop
Bush Womack TAP App Workshop

## Prerequisites

* Provision a Kubernetes cluster
* [Install TAP](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/index.html) in the cluster
* Install RabbitMQ cluster operator and create RabbitMQ cluster
    * Install [RabbitMQ cluster operator](https://github.com/rabbitmq/cluster-operator)
        * `kapp deploy --app rmq-operator --file https://github.com/rabbitmq/cluster-operator/releases/download/v1.11.1/cluster-operator.yml `

    * Assign permissions
        * `kubectl apply -f https://raw.githubusercontent.com/fjb4/dotnet-sensors-rabbit/master/k8s/rabbitmqcluster-read-write.yaml`

    * Create the "example-rabbitmq-cluster-1" RabbitMQ cluster that will be used by the .NET and Spring Sensors applications (wait for it to complete)
        * `kubectl apply -f https://raw.githubusercontent.com/fjb4/dotnet-sensors-rabbit/master/k8s/rabbitmq-cluster.yaml`

* Register the .NET Sensor application accelerator with TAP
    * `tanzu accelerator create dotnet-sensors-rabbit --git-repository https://github.com/fjb4/dotnet-sensors-rabbit --git-branch master`


### dotnet app
* register the app template in your AppAcc
* open your tap gui
* select spring-sensors-dotnet
* download the zip
* extract
* add to your git org
* tap deploy

### Create and deploy the SpringBoot Application that gets readings from the queue and displays them:
1. Open your TAP web Interface, for example:  [http://tap-gui.tap.jbush.io/create](http://tap-gui.tap.jbush.io/create)
3. Find the "Spring Sensors" application, and click the **"CHOOSE"** button
   * Optional, click **"VIEW REPOSITORY"** to review the template's repository
5. On the **"App Accelerator inputs"** page, change the **Name** to "sensors-demo". The rest of the fields can remain their defaults
   * Optional, click **"EXPLORE"** to review the template's project and structure
7. Click **"NEXT STEP"** to proceed
8. Review/verfiy the "App Accelerator inputs" and click **"CREATE"**
   * Optional, click **"EXPLORE ZIP FILE"** to review the template's project and structure
10. Click **"DOWNLOAD ZIP FILE"** to proceed. The zip file will be saved to your default download directory (often ~/Downloads)
12. Deploy the application via TAP:  
 ```tanzu app workload create sensors-demo --git-repo https://github.com/stwomack/sensors-demo --git-branch main --type web --service-ref "rmq=rabbitmq.com/v1beta1:RabbitmqCluster:default:example-rabbitmq-cluster-1"```
