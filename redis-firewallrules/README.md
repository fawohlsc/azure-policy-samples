# Redis Firewall Rules

Deploys Redis firewall rules and denies creating incompliant firewall rules. The policies are wrapped in an [ARM template](redis-firewallrules.json) to ease deployment. Deploying the ARM template to a management group of choice should result in the following:
- **Deny-Redis-FirewallRules** (*Policy definition*): Denies creating incompliant firewall rules.
- **Deny-Redis-FirewallRules** (*Policy assignment*): Assigns policy definition _Deny-Redis-FirewallRules_ to the management group.
- **Deploy-Redis-FirewallRule** (*Policy definition*): Deploys a single Redis firewall rule.
- **Deploy-Redis-FirewallRules** (*Policy set definition*): Groups multiple instances of _Deploy-Redis-FirewallRule_ into a policy set to deploy multiple Redis firewall rules.
- **Deploy-Redis-FirewallRules** (*Policy set assignment*): Assigns policy set definition _Deploy-Redis-FirewallRules_ to the management group.
- **Deploy-Redis-FirewallRules** (*Role assignment*):  Role assignment for the managed identity created during policy set assignment _Deploy-Redis-FirewallRules_.

## Try on Portal

[![Deploy To Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-aks%2Fazuredeploy.json)
[![Deploy To Azure US Gov](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazuregov.svg?sanitize=true)](https://portal.azure.us/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-aks%2Fazuredeploy.json)
[![Visualize](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/visualizebutton.svg?sanitize=true)](http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-aks%2Fazuredeploy.json)

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
    -TemplateFile ".\redis-firewallrules.json" `
    -TemplateParameterObject $templateParameterObject
```

## Try with CLI

```sh
az deployment mg create --location "northeurope" --management-group-id "fawohlsc" --template-file "./redis-firewallrules.json" --parameters \
"{ \
    'managementGroup': { \
        'value': 'fawohlsc' \
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

## Notes
- Describe that firewall rules are not nested inline properties in parent redis cache, so no array alias [*] Microsoft.Redis/firewallRules[*] - that's why policy set and copy properties https://docs.microsoft.com/en-us/azure/azure-resource-manager/templates/copy-properties
- Known issues: 
   - Deployment fails with code 'PrincipalNotFound'. Basically, the the creation of the managed identity during policy set assignment did not finish in time. Just rerun the deployment.
   - Deployment fails with code 'RoleAssignmentUpdateNotPermitted'. Deleting the policy set assignment does not clean-up the role assignments. Just delete the role assignments for 'Redis Contributor' at the management group.
