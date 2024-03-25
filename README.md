# sso-aks-authentication

Prerequisites
-------------
1. You need to be an Azure admin or have the admin rights to add the API permissions required for this setup.

**Step 1. Create AKS cluster**

a. Create a resource group 

- An Azure resource group is a logical group in which Azure resources are deployed and managed. When you create a resource group, you're prompted to specify a location.

    az group create --name myResourceGroup --location eastus

b. Create an AKS cluster

- To create an AKS cluster, use the az aks create command.

    az aks create --resource-group myResourceGroup --name myAKSCluster --enable-managed-identity --node-count 1 --generate-ssh-keys

After a few minutes, the command completes and returns JSON-formatted information about the cluster.

c. Connect to the cluster

- Configure kubectl to connect to your Kubernetes cluster using the az aks get-credentials command. This command downloads credentials and configures the Kubernetes CLI to use them.

    az aks get-credentials --resource-group myResourceGroup --name myAKSCluster

- Verify the connection to your cluster using the kubectl get command. This command returns a list of the cluster nodes.

    kubectl get nodes

**Step 2. Create AZ AD group and users**

Goto azure portal --> groups --> new groups

![image](https://github.com/tushardashpute/sso-aks-authentication/assets/74225291/c6ae11d3-20be-4438-8fba-2ee323c2e825)

![image](https://github.com/tushardashpute/sso-aks-authentication/assets/74225291/eeb35b38-fc1d-4285-9241-f79f493e9290)

The same created one more group DevOps to have read-only access to the cluster.

Now let's create users in Azure and add them to the groups that we have created.

Goto azure portal --> users 

![image](https://github.com/tushardashpute/sso-aks-authentication/assets/74225291/380bbbbd-dd96-4424-9868-1ce31b029208)

I have created two users eksadmin and eksreadonly.

![image](https://github.com/tushardashpute/sso-aks-authentication/assets/74225291/30e0fffb-cd61-4469-8f74-903b81cb19ee)

Let's add them to the admin and devops role respectively.

Goto azure portal --> groups --> select admin group --> member --> add member

![image](https://github.com/tushardashpute/sso-aks-authentication/assets/74225291/c58fade1-0472-4f73-8ba4-7381db00dd60)

![image](https://github.com/tushardashpute/sso-aks-authentication/assets/74225291/7571238d-4480-4575-8211-9debd9c6ea88)

![image](https://github.com/tushardashpute/sso-aks-authentication/assets/74225291/b9aa666a-ac73-41bf-940a-4803209af740)

**Step 3. Enable Azure-managed identity authentication for Kubernetes clusters with kubelogin**

 - Enable AKS-managed Microsoft Entra integration on your existing Kubernetes RBAC-enabled cluster using the az aks update command. Make sure to set your admin group to keep access to your cluster.

    az aks update -g myResourceGroup  -n myAKSCluster  --enable-aad --aad-admin-group-object-ids 4123d440-384b-4dd6-a8af-f4e85cbbbf53 --aad-tenant-id 5c50fb70-dbd8-4044-8f88-ae5bbcd63a75

References:
----------
- https://learn.microsoft.com/en-us/azure/aks/enable-authentication-microsoft-entra-id
- https://learn.microsoft.com/en-us/azure/aks/learn/quick-kubernetes-deploy-cli
