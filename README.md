# Azure CLI 2.0 Cheatsheet

Azure CLI 2.0 cheatsheet for Login, Resources, VMs, Resource groups, Storage, Batch, and Containers.

## Logging in

### Login with web
```
az login
```

### Login in CLI
```
az login -u myemail@address.com
```

### List accounts
```
az account list
```

### Set subscription
```
az account set --subscription "xxx"
```

## Listing locations and resources / general

### List all locations
```
az account list-locations
```

### List all my resource groups
```
az resource list
```

### Get what version of the CLI you have
```
azure --version
```

### Get help
```
azure help
```

## Creating a basic VM / Resource Group / Storage Account

### Get all available VM sizes
```
az vm list-sizes --location eastus
```

### Get all available VM images for Windows and Linux
```
az vm image list --output table
```

### Create a Linux VM
```
az vm create --resource-group myResourceGroup --name myVM --image ubuntults
```

### Create a Windows VM
```
az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter
```

### Create a Resource group
```
az group create --name myresourcegroup --location eastus
```

### Create a Storage account.
```
az storage account create -g myresourcegroup -n mystorageaccount -l eastus --sku Standard_LRS
```

## DELETING A RESOURCE GROUP

### Permanetly deletes a resource group
```
az group delete --name myResourceGroup
```

## Managing VM's

### List your VMs
```
az vm list
```

### Start a VM
```
az vm start --resource-group myResourceGroup --name myVM
```

### Stop a VM
```
az vm stop --resource-group myResourceGroup --name myVM
```

### Deallocate a VM
```
az vm deallocate --resource-group myResourceGroup --name myVM
```

### Restart a VM
```
az vm restart --resource-group myResourceGroup --name myVM
```

### Redeploy a VM
```
az vm redeploy --resource-group myResourceGroup --name myVM
```

### Delete a VM
```
az vm delete --resource-group myResourceGroup --name myVM
```

### Create image of a VM
```
az image create --resource-group myResourceGroup --source myVM --name myImage
```

### Create VM from image
```
az vm create --resource-group myResourceGroup --name myNewVM --image myImage
```

### List VM extensions
```
az vm extension list --resource-group azure-playground-resources --vm-name azure-playground-vm
```

### Delete VM extensions
```
az vm extension delete --resource-group azure-playground-resources --vm-name azure-playground-vm --name bootstrapper
```

## Managing Batch Account

### Create a Batch account.
```
az batch account create -g myresourcegroup -n mybatchaccount -l eastus
```

### Create a Storage account.
```
az storage account create -g myresourcegroup -n mystorageaccount -l eastus --sku Standard_LRS
```

### Associate Batch with storage account.
```
az batch account set -g myresourcegroup -n mybatchaccount --storage-account mystorageaccount
```

We can now authenticate directly against the account for further CLI interaction.

```
az batch account login -g myresourcegroup -n mybatchaccount
```

### Display the details of our created account.
```
az batch account show -g myresourcegroup -n mybatchaccount
```

### Create a new application.
```
az batch application create --resource-group myresourcegroup --name mybatchaccount --application-id myapp --display-name "My Application"
```

### Add zip files to application
```
az batch application package create --resource-group myresourcegroup --name mybatchaccount --application-id myapp --package-file my-application-exe.zip --version 1.0
```

### Assign the application package as the default version.
```
az batch application set --resource-group myresourcegroup --name mybatchaccount --application-id myapp --default-version 1.0
```

### Retrieve a list of available images and node agent SKUs.
```
az batch pool node-agent-skus list
```

### Create new Linux pool with VM config
```
az batch pool create \
    --id mypool-linux \
    --vm-size Standard_A1 \
    --image canonical:ubuntuserver:16.04.0-LTS \
    --node-agent-sku-id “batch.node.ubuntu 16.04”
```

### Now let's resize the pool to start up some VMs.
```
az batch pool resize --pool-id mypool-linux --target-dedicated 5
```

### We can check the status of the pool to see when it has finished resizing.
```
az batch pool show --pool-id mypool-linux
```

### List the compute nodes running in a pool.
```
az batch node list --pool-id mypool-linux
```

If a particular node in the pool is having issues, it can be rebooted or reimaged.
A typical node ID will be in the format 'tvm-xxxxxxxxxx_1-<timestamp>'.
```
az batch node reboot --pool-id mypool-linux --node-id tvm-123_1-20170316t000000z
```

### Re-allocate work to another node.
```
az batch node delete \
    --pool-id mypool-linux \
    --node-list tvm-123_1-20170316t000000z tvm-123_2-20170316t000000z \
    --node-deallocation-option requeue
```

### Create a new job to encapsulate the tasks that we want to add.
```
az batch job create --id myjob --pool-id mypool
```

### Add tasks to the job.
 …where <shell> is your preferred shell for execution (/bin/sh, /bin/bash, /bin/ksh etc.), and /path/to/script.sh is, of course, the full path of the shell script you’re invoking to get things started.

```
az batch task create --job-id myjob --task-id task1 --application-package-references myapp#1.0 --command-line "/bin/<shell> -c /path/to/script.sh"
```

### Add many tasks at once
```
az batch task create --job-id myjob --json-file tasks.json
```

Now that all the tasks are added - we can update the job so that it will automatically be marked as completed once all the tasks are finished.

```
az batch job set --job-id myjob --on-all-tasks-complete terminateJob
```

### Monitor the status of the job.
```
az batch job show --job-id myjob
```

### Monitor the status of a task.
```
az batch task show --job-id myjob --task-id task1
```

### Delete a job
```
az batch job delete --job-id myjob
```

## Managing Containers

If you HAVE AN SSH run this to create an Azure Container Service Cluster (~10 mins)

```
az acs create -n acs-cluster -g acsrg1 -d applink789
```

If you DO NOT HAVE AN SSH run this to create an Azure Container Service Cluster (~10 mins)

```
az acs create -n acs-cluster -g acsrg1 -d applink789 --generate-ssh-keys
```

### List clusters under your whole subscription
```
az acs list --output table
```

### List clusters in a resource group
```
az acs list -g acsrg1 --output table
```

### Display details of a container service cluster
```
az acs show -g acsrg1 -n acs-cluster --output list
```

### Scale using ACS
```
az acs scale -g acsrg1 -n acs-cluster --new-agent-count 4
```

### Delete a cluster
```
az acs delete -g acsrg1 -n acs-cluster
```
