# Azure Policy Samples
The purpose of this repository is to collect some of my Azure Policy samples. Before applying the policies to your environment please properly check and test them. Also take a look at [Azure/Azure-Policy](https://github.com/Azure/azure-policy) and [Azure/Community-Policy](https://github.com/Azure/Community-Policy/), which are great additional sources for Azure Policy. Happy coding!

List of samples:
- [redis-firewallrules](./redis-firewallrules) - Deploys Redis firewall rules and denies creating incompliant firewall rules.

List of samples, which I published on [Azure/Community-Policy](https://github.com/Azure/Community-Policy/):
- [modify-subnet-routetable](https://github.com/Azure/Community-Policy/tree/master/Policies/Network/modify-subnet-routetable) - Adds the default route table to subnets. Other route tables are replaced with the default route table.
- [modify-routetable-nexthopvirtualappliance](https://github.com/Azure/Community-Policy/tree/master/Policies/Network/modify-routetable-nexthopvirtualappliance) - Adds route with address prefix 0.0.0.0/0 pointing to the virtual appliance in case there is none.
- [deny-route-nexthopvirtualappliance](https://github.com/Azure/Community-Policy/tree/master/Policies/Network/deny-route-nexthopvirtualappliance) - Deny route with address prefix 0.0.0.0/0 not pointing to the virtual appliance.
- [deny-route-nexthopinternet](https://github.com/Azure/Community-Policy/tree/master/Policies/Network/deny-route-nexthopinternet) - Deny route with next hop type internet.
- [modify-subnet-nsg](https://github.com/Azure/Community-Policy/tree/master/Policies/Network/modify-subnet-nsg) - Adds the default network security group to subnets in case there is none.
