# Enhancing Applications

## Project Overview
With a combination of cloud technologies, such as Azure Kubernetes Service, VM Scale Sets, Application Insights, Azure Log Analytics, and Azure Runbooks, I collected and displayed performance and health data about an application, and implemented automated remediation tasks. The project showcased my skills in diagnosing and rectifying application and infrastructure problems. Below are the project steps:
- Setup Application Insights monitoring on a VMSS and implement monitoring in an application to collect telemetry data
- Setup an auto-scaling for a VMSS
- Setup a VMSS alert and create a RunBook to automate the resolution of performance issues
- Create alerts to trigger auto-scaling on an AKS cluster and trigger a RunBook to execute

## Dependencies
1. [Azure Account](https://azure.microsoft.com/en-us/free/)
2. [Azure DevOps account](https://azure.microsoft.com/en-us/pricing/details/devops/azure-devops-services/) (Click **Start Free** under **Azure Pipelines**)
3. [VS Code or your preferred editor](https://code.visualstudio.com/Download) Install the VS Code extensions for Python (optional)
4. [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest)
5. Redis
   - [Redis—Non-Windows Download](https://redis.io/download)
   - [Redis—Windows Download](https://riptutorial.com/redis/example/29962/installing-and-running-redis-server-on-windows)
   - [Redis Quick Start](https://redis.io/topics/quickstart)

## Deploy the webapp in Azure
1. Log in to Azure using `az login`. Run `./vmss.sh` in the terminal. Check the Public IP address. 
   ```
   az vmss list-instance-connection-info \
   --resource-group group20210405 \
   --name vmss20210405
   ```
2. Connect to the VM. 
   ```
   ssh -p [port] admin20210405@[public-ip]
   ```
3. Use Azure Pipelines to deploy the application to the Azure VM Scale Set. Follow the step-by-step instructions [here](azure-pipelines-instructions.md).

## Application Insights & Log Analytics
1. Create an Application Insights resource with the Log Analytics workspace
2. Enable Application Insights monitoring for the VM Scale Set
![](screenshots/application-insights/vmss20210405_1.png)
![](screenshots/application-insights/vmss20210405_2.png)
3. Add the instrumentation key of Application Insights to `main.py` 
5. View custom event telemetry when 'Dogs' is clicked and when 'Cats' is clicked.
![](screenshots/application-insights/event.png)
6. Create a query to view the event telemetry in Log Analytics.
![](screenshots/application-insights/trace_query_result.png)
7. Create a chart from query showing when 'Dogs' or 'Cats' is clicked.
![](screenshots/application-insights/trace_query_chart.png)

## Autoscaling
1. For the VM Scale Set, create an autoscaling rule based on metrics.
![](screenshots/autoscaling-vmss/scaling_rule.png)
2. Trigger the conditions for the rule, causing an autoscaling event.
![](screenshots/autoscaling-vmss/new_instance.png)
3. When complete, enable manual scale.

### Runbook
1. Create an alert which uses a runbook to remedy a problem.
![](screenshots/runbook/alert_config_4.png)

## Monitoring Containers
1. Run `az login` to login, then run `./cluster.sh` to create an AKS cluster and deploy a container to it.
2. Once that is completed, go to Insights for the cluster.
3. Observe the state of the cluster. Note the number of nodes and number of containers.
![](screenshots/kubernetes-cluster/cluster_state.png)
4. Create an alert in Azure Monitor to trigger when the number of pods increases over a certain threshold.
![](screenshots/kubernetes-cluster/alert_rule.png)
5. Create an autoscaler by using the following Azure CLI command—`kubectl autoscale deployment azure-vote-front --cpu-percent=70 --min=1 --max=10`
6. Cause load on the system. Run the following command:
   ```
   kubectl run -it --rm load-generator --image=busybox /bin/sh
   ```
   Retrieve External IP address of the cluster by running `kubectl get services`. Then run the following command:

   ```
   while true; do wget -q -O- [External IP]; done
   ```
7. After approximately 10 minutes, stop the load.
8. Observe the state of the cluster. Note the number of pods; it should have increased and should now be decreasing.
![](screenshots/kubernetes-cluster/cluter_state_after_scaling.png)
