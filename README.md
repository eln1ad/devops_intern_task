# Devops Intern Task

## Part 1 - creating resource group & service principal

```bash
# login to azure
az login

RG_LOCATION="northeurope"
RG_NAME=<Choose a name> # this will be saved as a github actions secret
SP_NAME=<Choose a name> # this will also be saved as a github actions secret
ROLE="contributor" # this is a built-in role provided by azure, I choose contributor because it also has write access
SUBSCRIPTION_ID=$(az account show --query id --output tsv) # query my user info

# create resource group
az group create --name $RG_NAME --location $RG_LOCATION

# create service provider, so that I can sign in later
# the output is the appId, password and tenant, these will be saved as github actions secrets
az ad sp create-for-rbac --name $SP_NAME \
                        --role $ROLE \
                        --scopes /subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RG_NAME

az logout
```

## Part 2 - logging in using service principal & create virtual machine (will be used inside actions)

```bash
TODAY="$(date +'%d-%m-%Y')"
VM_NAME="devops-intern-vm-$TODAY"
SP_APP_ID=<This will be in github actions secrets>
SP_PASSWORD=<This will be in github actions secrets>
SP_TENANT=<This will be in github actions secrets>

az login --service-principal \
        --username $SP_APP_ID \
        --password $SP_PASSWORD \
        --tenant $SP_TENANT

az vm create --name $VM_NAME \
            --resource-group $RG_NAME \
            --image Ubuntu2204 \
            --generate-ssh-keys \
            --admin-username $ADMIN_USERNAME \
            --admin-password $ADMIN_PASSWORD # password must be at least 12 length, with 1 number and a special character
```

