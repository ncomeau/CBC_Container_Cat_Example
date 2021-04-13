# CBC_Container_Cat_Example


This repository is intended to highlight, and simplify, the process for building, and running, an demo example comprised of multiple varying k8 deployments. This demo is intended to showcase one of the many capabilities of the CBC Container Security offering; configuration controls for optimal k8 hygiene & security. 

Click Below image for full video breakdown.

[![CBC_Cat_Demo_Vid](https://i9.ytimg.com/vi/Tay6iOislKU/mq1.jpg?sqp=CNiE14MG&rs=AOn4CLD-vkhsTtmg4JsVjYyjXjl0IqwLlw)](https://youtu.be/Tay6iOislKU)


## Setup Instructions

### Environment Setup
1. Download server ISO from Ubuntu's [page.](https://releases.ubuntu.com/20.04/)
    * Be certain to download one with a GUI, for demo purposes
    
2. Create the VM.
    * Do not use 'Easy Install' option
    * I suggest 4GB of RAM, 2 CPU cores, and 40GB storage/HD
    * Ideally VMware Tools is installed
    
3. _(Optional)_ Once VM is created, select ‘Activities’ bar on the upper left-hand corner.
    * In the search window; type ‘Terminal’
      * Drag and drop over to your ‘Favorites’ in the left-hand navigation panel
    * In the search window; type ‘Power’
      * Select the ‘Power’ option under ‘Settings’
      * Adjust ‘Blank Screen’ drop down to either ‘15 Minutes’ or ‘Never’
      
4. Download, or git, the [k8_demo_setup.sh]() listed above, and move to your Ubuntu VM.
    * NOTE: If you copy and paste you might run into errors with special characters being added to the script
    
5. Open terminal window on your Ubuntu Machine
    * Run 'sudo -i' and input in your user password to achieve root access
    
6. Navigate to the location of the script you downloaded/copied above.

7. Run the script with the following command: _sh k8_demo_setup.sh_

8. Upon completion of the script run 'source  ~/.bashrc' in your terminal window

9. Everything should be all set for your demo env! You should have:
      * Microk8s installed
      * Docker installed
      * Git installed
      * Microk8s registry and dns enabled
      * Docker image created and pushed to k8s registry
      * 'feline' namespace created w/Dummy-Workload.yaml deployed
      * A dir called cat-demo
      * 3 yamls for the demo
      * A cleanup script to run during the demo, called demo_cleanup.sh

### CB Container Security Setup

1. Log into your CBC instance

2. Navigate to \[Inventory > Kubernetes > K8s Clusters > Add Cluster\] and install the CB Dataplane on your Cluster

3. Upon completion of installation, navigate to \[Inventory > Kubernetes > K8s Scopes > Add Scope\]
      * Create a new scope, based on the newly created 'feline' namespace

4. Upon completion of creating a scope, navigate to \[Enforce > K8s Policies > Add Policy\]
      * Create a new policy with the following configs;
            * Scope: Scope you created in prior step
            * Network: Node Pode --> Alert
            * Quota: Memory Limits --> Block
            * Quota: CPU Limits --> Block

5. (_Optional_)Upon completion of policy creation - it is recommended that you create a notification tied to a Slack channel
      * Navigate to \[Settings > Notifications > Kubernetes tab > Add Notification\]
      * Add Notification with the following params:
            * Polices: Policy created in step above
            * Provider URL: Slack Webhook
                  * See https://api.slack.com/messaging/webhooks for additional details on using Slack webhooks

## Demo 

The intent of the demo is ability to highlight enforcement and adherence to best practices in container creation - ensure you run the 'demo_cleanup.sh' after each attempted deployment of one of the cat yamls.


   * bad-cat-deployment.yaml;
     * Will block deployment creation, due to violation of lack of CPU & Mem limits. 
     * Will allow service to be created, but will generate an alert for node port violation.
        * Associated blocks & alerts can be seen in the CBC console under \[Harden > K8s Violations\] and in Slack (if notifications enabled in step 5 above).
   * good-cat-deployment.yaml:
     * CPU & Mem limits added to the yaml - deployment will be created with no violations.
     * Will allow service to be created, but will generate an alert for node port violation.
       *  Associated alert can be seen in the CBC console under \[Harden > K8s Violations\] and in Slack (if notifications enabled in step 5 above).
   * best-cat-deployment.yaml:
     * CPU & Mem limits added to the yaml - deployment will be created with no violations.
     * Node Port removed from yaml - service will be created with no violations
 
Lastly, if you navigate to the IP:Port specified when creating the best-cat-deployment.yaml (kubectl get all -A), you will see a humorous random gif of the cat!



    
      
      

    
