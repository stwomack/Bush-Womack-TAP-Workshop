# Bush Womack TAP App Workshop
Bush Womack TAP App Workshop

## Script Steps, John:
1. Start with Lead Dotnet Engineer. I'm John, I'm good at dotnet, but new to k8s
2. Architecture team has decided on RMQ for messaging. I'll install that into a k8s cluster for us to use
3. Architecture team also provided this Templates and incorporated our org's best practices
4. Create RabbitMQ service instance
5. Show AppAcc Template and how this sensor data publisher was built & added to AppAcc for others to review & use
6. Show workshop yaml /tap/workload.yaml
7. Add project to github rep
8. Deploy via TAP
9. Single sensor data only 

## Script Steps, Steve:
1. Start with Spring/Java App Developer. I'm Steve, I'm good at Dev, but new to K8s
2. PM needs a sensor reading app that displays temp/pressure measurements - from ONE sensor emitting temp and pressure 
3. I'm told by John that the platform team has already created a RabbitMQ instance for me and I'm good to bind my app to it for messages
4. Open App-Accellerator, select Spring-Sensors
5. Add project to github repo
6. Deploy via TAP

## Script Steps, John:
1. Iterate - Multiple sensors, not just the one we had initially
2. Change , commit, watch rebuild & deploy
3. Show RMQ mgmt GUI

## Script Steps, Steve:
1. Show that there's two sensors now
2. PM asked for less precision, so round 'em up
4. Redeploy via TAP & show
