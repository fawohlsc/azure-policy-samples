# Redis Firewall Rules

Deploys Redis firewall rules and denies creating incompliant firewall rules. The policies are wrapped in an [ARM template](redis-firewallrules.json) to ease deployment. Deploying the ARM template to a management group of choice will result in the following:
- **Policy Definitions**
  - **Deny-Redis-FirewallRules**: Denies creating incompliant firewall rules.
  - **Deploy-Redis-FirewallRule**: Deploys a single Redis firewall rule.
- **Policy Set Definitions**
  - **Deploy-Redis-FirewallRules**: Groups multiple instances of _Deploy-Redis-FirewallRule_ into a policy set to deploy multiple Redis firewall rules.
- **Policy Assignments**
  - **Deny-Redis-FirewallRules**: Assigns policy definition _Deny-Redis-FirewallRules_ to the management group.
  - **Deploy-Redis-FirewallRules**: Assigns policy set definition _Deploy-Redis-FirewallRules_ to the management group.
- **Role Assignments**
   - **Deploy-Redis-FirewallRules**: Role assignment for the managed identity created during policy assignment _Deploy-Redis-FirewallRules_.

## Try on Portal

[![Deploy To Azure](https://raw.githubusercontent.com/fawohlsc/azure-policy-samples/main/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Ffawohlsc%2Fazure-policy-samples%2Fmain%2Fredis-firewallrules%2Fazuredeploy.json)
[![Deploy To Azure US Gov](https://raw.githubusercontent.com/fawohlsc/azure-policy-samples/main/images/deploytoazuregov.svg?sanitize=true)](https://portal.azure.us/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Ffawohlsc%2Fazure-policy-samples%2Fmain%2Fredis-firewallrules%2Fazuredeploy.json)

> In case you face any errors during deployment, please see [Known Issues](#known-issues).

## Try with PowerShell

```powershell
$managementGroup = "managementgroup1"
$location = "northeurope"
$templateParameterObject = @{
    "managementGroup" = $managementGroup;
    "firewallRules"   = @(
        @{
            "name"    = "rule1";
            "startIP" = "10.0.0.0";
            "endIP"   = "10.1.0.0"
        },
        @{
            "name"    = "rule2";
            "startIP" = "20.0.0.0";
            "endIP"   = "20.2.0.0"
        }
    )
}

New-AzManagementGroupDeployment `
    -Location $location `
    -ManagementGroupId $managementGroup `
    -TemplateUri "https://raw.githubusercontent.com/fawohlsc/azure-policy-samples/main/redis-firewallrules/azuredeploy.json" `
    -TemplateParameterObject $templateParameterObject
```

> In case you face any errors during deployment, please see [Known Issues](#known-issues).

## Try with CLI

```sh
az deployment mg create --location "northeurope" --management-group-id "managementgroup1" --template-uri "https://raw.githubusercontent.com/fawohlsc/azure-policy-samples/main/redis-firewallrules/azuredeploy.json" --parameters \
"{ \
    'managementGroup': { \
        'value': 'managementgroup1' \
    }, \
    'firewallRules': { \
        'value': [ \
            { \
                'name': 'rule1', \
                'startIP': '10.0.0.0', \
                'endIP': '10.1.0.0' \
            }, \
            { \
                'name': 'rule2', \
                'startIP': '20.0.0.0', \
                'endIP': '20.2.0.0' \
            } \
        ] \
    }, \
}"
```

> In case you face any errors during deployment, please see [Known Issues](#known-issues).

## Known Issues
- **PrincipalNotFound** - For remediating resources, a managed identity is created during policy assignment **Deploy-Redis-FirewallRules**. In case Azure Active Directory did not complete the provisioning of the managed identity in time, the role assignment **Deploy-Redis-FirewallRules** will fail. Just rerun the deployment to resolve the issue.
- **RoleAssignmentUpdateNotPermitted** - When you delete the policy assignment for **Deploy-Redis-FirewallRules**, the  managed identity is deleted as well. Unfortunately, the deletion does not include the role assignment for the managed identity. You have to manually delete the role assignment with role *Redis Contributor* at the management group before redeploying the ARM template.

## Notes
- Describe that firewall rules are not nested inline properties in parent redis cache, so no array alias [*] Microsoft.Redis/firewallRules[*] - that's why policy set and copy properties https://docs.microsoft.com/en-us/azure/azure-resource-manager/templates/copy-properties
- Known issues: 
   - Deployment fails with code 'PrincipalNotFound'. Basically, the the creation of the managed identity during policy set assignment did not finish in time. Just rerun the deployment.
   - Deployment fails with code 'RoleAssignmentUpdateNotPermitted'. Deleting the policy set assignment does not clean-up the role assignments. Just delete the role assignments for *Redis Contributor* at the management group.
