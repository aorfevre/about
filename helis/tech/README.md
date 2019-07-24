---
description: Overview of our payment protocol
---

# Protocol

**Payment Contracts**

Helis’ payment contracts enable powerful payment mechanisms such as scheduled payments, recurring payments or batched payments. Our contracts have been written in a way where any new payment mechanism can be easily added to the protocol for whatever use cases are required.

 **Architecture**

The payments protocol is run on the Ethereum blockchain through smart contracts written in Solidity. Standard gas fees apply to interact with the smart contract for businesses, and consumers. Apart from the transaction fee made during a payment no additional costs are applied. Considerations have been made to ensure expensive operations such as SSTORE are minimised where possible.

![](https://lh5.googleusercontent.com/bbS_Lc4Ef-CGbZvZrCyTxl2OxxL_BXnccmyj9Y4NMBz0aPWUiYXGCMTscaT6cgP54arVoKOjLCL3z8qFvbbxcVr2HEPqZBAtb7c16Gpvfk0avWigGUCDmVb84KgBo91QB_Rnl1M)

**Transfer proxy**

By having a single component, the transfer proxy, which is authorised and responsible for taking and making payments we can store the logic for these payments in the executor contract. While the logic for payments should be tied to the information returned from the collectible interface, having a hard coupling could lead to costly immutability down the line. The transfer proxy has an array of authorised addresses which grant access to pull funds from users and pay businesses although this is controlled by a multi-signature wallet with a time lock of 2 weeks to propagate changes. The only exception to the time-lock is to kill the contract to revoke access to user funds in the case of an attack By having multiple authorized addresses, a new executor contract can be deployed and the old one can be deprecated. When the transfer proxy is called, it uses the ERC20 transferFrom\(\) function to send DAI directly from the user’s wallet to the merchant. 

**Executor**

The executor component is where the core logic and functionality of the smart contract lies. Consumers and service nodes interact with it directly in order to claim and make payments. Since all subscription contracts adhere to the collectible interface, the logic for how much money should be charged and whether the subscription is valid is in the actual subscription contract. The service node simply interacts with the exposed public methods. The only extra power the service node has is to cancel a user’s payment in the case that they don’t have enough funds. When a user subscribes to a payment option, the executor calls the transfer proxy to facilitate the transaction and adds the payment to the transaction registry.

**Collectible**

Initial plans for the architecture included a subscription contract and plan contract for businesses and consumers to interact with. Although this creates rigidity when something like a donation subscription contract needs to be implemented as the user is in full control of how much they want to give rather than the merchant setting a predetermined amount. For this reason a more general purpose architecture has been made which facilitates the addition of new subscription contracts as long as they adhere to the interface. Currently the interface methods include:

1. Check whether the payment is valid.

2. Get the payee’s balance.

3. Return how much the payee owes from their payment .

4. Terminate the payment if they don’t have enough funds.

If a payee doesn’t have enough Dai to pay for their subscriptions, an email will be sent to them to remind them to top up. These email details and reminders are to be hosted on a centralised server due to the unwanted nature of linking an email address to public key. 

 **Subscription Registry**

The subscription registry allows users to view all the services they’re currently subscribed to through mappings stored in the contract. Upcoming payments can be viewed and cancelled at any time. Blockchain technology provides a far more superior experience as a user isn’t required to ﬁnd the ”cancel payment” button on a vendor’s website since they can call the smart contract directly.  
****

