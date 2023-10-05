# Repository scan

This folder contains a collection of workflows that utilize CyDig controls for repository scans in for GitHub projects.

## How to use the workflow template for a repository scan

The template workflow named [compliance-template.yml](https://github.com/Omegapoint/cydig-compliance/blob/main/github/repository%20scan/compliance-template.yml) is a template workflow to be called from other workflows in repositories where you want to run the compliance actions/controls.

[sample-compliance-workflow.yml](https://github.com/Omegapoint/cydig-compliance/blob/main/github/repository%20scan/sample-compliance-workflow.yml) is a sample workflow that can be used as a sample for the workflow to be created in all repos where you want to query the compliance state. This workflow will call the template workflow above.

[cydigConfig.json](https://github.com/Omegapoint/cydig-compliance/blob/main/github/cydigConfig.json) is a configuration file used for repository scan. This file sets parameters globally for the scans, if you do not set separate values in your repositories for repository scan.

### Getting started

1. Create a repository in your GitHub Organization called "compliance" and add the template workflow [**compliance-template.yml**](https://github.com/Omegapoint/cydig-compliance/blob/main/github/repository%20scan/compliance-template.yml) to it.

2. Add the file [cydigConfig.json](https://github.com/Omegapoint/cydig-compliance/blob/main/github/cydigConfig.json) to your newly created repository. Go through the configuration file and change the file to suit your project. Read [here](https://github.com/Omegapoint/cydig-compliance/blob/main/github/readme-cydig-config.md) for more information about the configuration.

3. If some parameters in some of your repos differ from the configuration files value, you can send them in as input parameters to the call of the workflow for one specific repository. Read [here](https://github.com/Omegapoint/cydig-compliance/blob/main/github/repository%20scan/readme-repository-scan.md#change-input-parameters-for-specific-repositories) for more information.

4. Trigger the new workflow "Compliance Workflow".

5. Copy the markdown links from the **Upload Compliance State** build step artifact output into the top of any README.md where you want to display the compliance badges. You only need to do this after the first run or whenever there is a change in the design of the badges.

## Compliance Controls

The compliance controls that are currently available are listed below.

### Azure Compliance Controls

* Azure Secure Score
* Number of users in a subscription (production) in Azure
* Number of deployed VMs in Azure
* Azure resources in EU only

### Repository Compliance Controls

* Number of reviewers on a pull request
* Date of latest threat modeling 
* Date of latest penetration test 

## Secrets
[Read more here on how to create a GitHub secret for a repository.](https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions#creating-secrets-for-a-repository)

To be able to run CyDig controls to your repositories, you need to set up the following variablers as secrets (either as repository or organization secrets):

* AZURE_CLIENT_ID 
* AZURE_TENANT_ID
* AZURE_SUBSCRIPTION_ID
* MY_GITHUB_PAT
* CYDIG_API_KEY

Here's a brief description of each secret varibale to be set up:
### **AZURE_CLIENT_ID**, **AZURE_TENANT_ID**, **AZURE_SUBSCRIPTION_ID**

You need to first configure trust between the GitHub workflow and an Azure Managed Identity or an Azure AD App (Service Principal). [More information can be found here.](https://github.com/marketplace/actions/azure-login). Follow these steps: ([or read this documentation](https://learn.microsoft.com/en-us/azure/active-directory/workload-identities/workload-identity-federation-create-trust?pivots=identity-wif-apps-methods-azp#configure-a-federated-identity-credential-on-an-app)).

1. Create an app registration in Azure Portal, and make sure the app registration has a role in your subscription. 
2. Select `Certificates & secrets` in your app registration, select the `Federated credentials tab`, and select `Add credential`.
3. Specify the Organization and Repository for your GitHub Actions workflow. For Entity type, select Environment, Branch, Pull request, or Tag and specify the value. The values must exactly match the configuration in the GitHub workflow. 
4. Add a Name for the federated credential.
5. Select Add to configure the federated credential.

Save the following values from your Microsoft Entra application registration for your GitHub workflow, as secrets:
- AZURE_CLIENT_ID: the Application (client) ID
- AZURE_TENANT_ID: the Directory (tenant) ID
- AZURE_SUBSCRIPTION_ID: your subscription ID

When logging in to azure in the workflow, there values are used, as seen below.
```yaml
-   name: 'Az CLI login'
    uses: azure/login@v1
    with:
        client-id: ${{ secrets.AZURE_CLIENT_ID }}
        tenant-id: ${{ secrets.AZURE_TENANT_ID }}
        subscription-id: ${{ secrets.AZURE_SUBSCRIPTION_ID }}
```

### **MY_GITHUB_PAT**

When calling the Compliance control action one of the input values required are a PAT, as seen below.
```yaml
  - name: 'Run Compliance Controls'
    uses:  Omegapoint/cydig-compliance-action@v1
    with:
      PAT-token: ${{ secrets.MY_GITHUB_PAT }}
      cydigConfigPath: ${{ env.configFile }}
```
When creating a PAT you only need to scope **public_repo** under the **repo** section, as seen below. After created add the token as a secret to your repoistory and use the name you choose for the secret when calling the action, as shown bellow:

![image](https://github.com/Omegapoint/cydig-compliance/assets/71070272/535eb98f-be3b-4e45-928f-81f4c9196096)

### **CYDIG_API_KEY**
The CyDig team will share this secret with you only once, and it is crucial that you protect it, as it is connected to you and should not be visible to others.

## Change Input Parameters for Specific Repositories

If any or some repositories needs different values than the ones specified in the configuration file, these values can be sent in as input parameters to the template. Some examples on when you need to send in parameters to the compliance template:

* You are not doing a threat modeling of penetration test on the whole system, just a subset of your repositories. You would then call the template as shown below:

```yaml
 CyDig-Compliance-Workflow:
    uses: Omegapoint/compliance/.github/workflows/compliance-template.yml@main 
    with:
        threatModelingDate: "2023-09-20"
        pentestDate:  "2023-08-30"
    secrets: inherit
```

The parameters that can be changed are listed in the [compliance-template.yml](https://github.com/Omegapoint/cydig-compliance/blob/main/repository%20scan/compliance-template.yml) and shown below:

```yaml
inputs:
    threatModelingDate:
        type: string
    threatModelingTag:
        type: string
    pentestDate:
        type: string
    pentestTag:
        type: string
    scaTool:
        type: string
    sastTool:
        type: string
    codeQualityTool:
        type: string
    nameOfBoard:
        type: string
```
