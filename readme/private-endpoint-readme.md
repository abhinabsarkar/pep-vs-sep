# Private Endpoint 
Azure Private Endpoint is a network interface that connects you privately and securely to a service powered by Azure Private Link. Private Endpoint uses a private IP address from your VNet, effectively bringing the service into your VNet. The service could be an Azure service such as Azure Storage, Azure Cosmos DB, SQL, etc. or your own Private Link Service.

![Alt text](/images/private-endpoint.png)

Features:
* Routing through Azure backbone network via Private Link service
* Enabled for one instance of PaaS resource type
* Security against data exfiltration is in-built
* Access from On-Premise via ExpressRoute private peering
* PaaS resource gets a Private IP
* Outbound internet is not required at the NSG level for connection
* Available for all the services or any service which is fronted by a load balancer
* Charged hourly and also by the amount of inbound and outbound data processed

When Private Endpoint is created for a PaaS resource (destination) like Azure Key Vault, it assigns a Private IP to the resource from the Subnet (source). This basically means that the resource is brought into the VNet & connection to the resource happens over the Private Link Service. If you look into the logs, you will find that both the source & destination (PaaS resource) will be having Private IP address.

![Alt text](/images/nslookup-pep.png)

Private Endpoint is a one to one connection which originates from a subnet & applies to a specific instance of a resource type i.e. one instance of a given Service (resource type).