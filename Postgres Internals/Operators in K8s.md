#### Custom Resource Definitions
Apart from the usual resources like deployments, secrets etc which are resources provided by K8s we can also extend this resource and create new resources using the Operator Framework. Most of the CNCF projects are operators on top of K8s
Deploying the CRD and Custom Controller is the responsibility of the devops team
To Extend we have the following resources
1. CRD: Defining a new type of API to K8s. This would be a yaml file which would contain information about all the fields the user can submit in the Custom Resource. For example a deployment.yaml contains the actual values for deployment spec which in the custom case is called CRD
2. CR: The actual instantiation of the CRD by filling in the required fields from YAML
3. Custom Controller: Inside kubernetes we have something called as a deployment controller. This takes care of creating replica set. Thus in teh K8s runtime we need a custom controller knows what to do.
A custom controller interacts with the api-server to setup listers and watchers. Thus a custom controller waits on a list of watchers for creating resources and trigger actions. This custom controller adds new watchers

Replica Set is also a controller which maintains the state of a kubernetes pod


##### Operators 
These are used to bundle/package and manage a K8s controller
Operators are better than Helm in the prospect of reconcilation where if the CR deployment changes config by mistake operator keeps a tab on that and overrides the wrong changes.