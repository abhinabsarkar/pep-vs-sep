# Service Endpoint
Virtual Network (VNet) service endpoint provides secure and direct connectivity to Azure services over an optimized route over the Azure backbone network. Endpoints allow you to secure your critical Azure service resources to only your virtual networks. Service Endpoints enables private IP addresses in the VNet to reach the endpoint of an Azure service without needing a public IP address on the VNet.

![Alt text](/images/service-endpoint.png)

Feature:
* It is enabled for a Subnet within a VNet
* It is enabled for Azure Service like Key Vault, Storage, SQL, etc.
* Once enabled, applies to all resources of the given resource type from the subnet i.e. all instances of the PaaS Service (resource type) are enabled.
* Traffic goes through the backbone network & the network hops are reduced.
* Allows access from On-Premise via ExpressRoute Microsoft peering or public peering
* On the NSG, if outbound connectivity to internet is denied from the VNet, it requires connectivity to PaaS resources to be enabled
* No cost. It is enabled on the VNet.
* Security against data exfiltration enabled using Endpoint policies 

With service endpoints, the source IP address for service traffic from the subnet will switch from using public IPv4 addresses to using private IPv4 address. Existing IP firewall rules using Azure public IP addresses will stop working with this switch. If you look into the logs, you will find the source is using the Private IP address whereas the destination will be the Public IP address of the PaaS resource.

![Alt text](/images/nslookup-sep.png)

## How it works behind the scenes?
When Service Endpoint is enabled for a Subnet within a Virtual Network i.e. VNet --> Service Endpoint --> Add Azure Service --> Subnet, behind the scenes it creates an effective route with next hop type as *VirtualNetworkServiceEndpoint*. This can be seen by going to the VM --> NIC --> Support + troubleshooting --> Effective routes.

![Alt text](/images/sep-route.png)

> The Azure DNS IP address is 168.63.129.16. This is a static IP address and will not change.

A new route added (highlighted) has been inherited from the subnet. This new route includes the **entire public IP range for the Azure Key Vault service** and instructs traffic that in order to get to anything within these ranges, it should go via a “virtual network service endpoint”. This will be an ingress into the Azure fabric which will forward the traffic to the Azure Key Vault instance rather than going out and via the public endpoint as it would have done before.

> *Using service endpoints does not remove the public endpoint from Azure Key Vault or Azure Storage accounts – it’s just a redirection of traffic. So, while we have traffic over the new virtual network service endpoint for a particular subnet, anything outside of that subnet will still access the Azure Key Vault or Storage account over the public endpoint(s); so we need to have our white/black listed appropriately for these resources.*

Azure selects the route type, based on the following priority:
* User-defined route
* BGP route
* System route

> *System routes for traffic related to virtual network, virtual network peering, or virtual network service endpoints, are preferred routes, even if BGP routes are more specific.*

## How routing works in Azure?

[Egress network traffic in Azure](https://docs.microsoft.com/en-us/azure/azure-australia/gateway-egress-traffic#architecture) 

[Azure traffic routing - video explanation](https://www.youtube.com/watch?v=tXLScLO-DRI)