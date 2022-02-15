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
* Open your TAP web Interface, for example:  [http://tap-gui.tap.jbush.io/create](http://tap-gui.tap.jbush.io/create)
* Find the "Spring Sensors" application, and click the **"CHOOSE"** button
   * Optional, click **"VIEW REPOSITORY"** to review the template's repository
* On the **"App Accelerator inputs"** page, change the **Name** to "sensors-demo". The rest of the fields can remain their defaults
   * Optional, click **"EXPLORE"** to review the template's project and structure
* Click **"NEXT STEP"** to proceed
* Review/verfiy the "App Accelerator inputs" and click **"CREATE"**
   * Optional, click **"EXPLORE ZIP FILE"** to review the template's project and structure
* Click **"DOWNLOAD ZIP FILE"** to proceed. The zip file will be saved to your default download directory (often ~/Downloads)
* Extract the zip file using whatever method you typically use. For example `unzip sensors-demo.zip`
* Initialize a local git repository, commit your code, and push to GitHub
   * `git init`
   * `git add -A .`
   * `git commit -m "initial commit"`
   * `git remote add origin git@github.com:<GITHUB-REPO-URL>.git`
   * `git branch -M main`
   * `git push -u origin main`
* Using your usual IDE or terminal, add the project to your newly created repo
* Deploy the application via TAP (substitute your actual repo url in when running the following command):  
 ```tanzu app workload create sensors-demo --git-repo https://github.com/{your-repo}/sensors-demo --git-branch main --type web --service-ref "rmq=rabbitmq.com/v1beta1:RabbitmqCluster:default:example-rabbitmq-cluster-1"```
   * In a terminal window, tail the application as TAP builds and deploys it:    
   `tanzu apps workload tail sensors-demo`
* Verify that the application is running by visiting its url, for example http://sensors-demo.default.tap.jbush.io/
