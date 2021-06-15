+++
title = "Lightning Network consulting"
date = 2021-06-14
+++
I often receive requests for consulting on services related to Bitcoin/Lightning, these are basically the following:

**Custodial lightning services:**

* Allow to clients to use my nodes in order to implement lightning network on client's business
* Implementing a lightning network API that connects the client with one of my nodes, the API will have some basic endpoints and user custom endpoints:
	- Invoice creation
	- Lookup payments
	- Withdraw
	- Get invoices
	- Get user balance
	- Build lnurl
	- Get lnurl orderId
	- Get lnurl withdraw
	- Check lnurl by orderId
* Implementing the frontend that will work with the API

**Non custodial lightning services:**

* Creating a Bitcoin/Lightning infrastructure for clients in order to allow them to have the total custody of their funds.
	- Lightning node on demand, it's recommended to have only one lightning node
	- Maintenance of the lightning node:
		+ Open channels
		+ Close unresponsive channels
		+ Keep rebalanced the channels depending on the user needs, for example, if the user have an e-commerce, it only needs to keep inbound capacity all the time, this should be automatized
		+ Loop In/Out

The same API services mentioned on the custodial section are offered on this non custodial services.

Security:
Although this field is very new and it is difficult to consider someone an expert, I have been working with these types of lightning applications for 3 years, in which I have learned about good practices when it comes to security.

Lightning nodes are running on Linux, operative system which I've been using since more than 20 years, and managing linux security is something that I'm familiar.
