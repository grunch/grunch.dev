+++
title = "@LNp2pBot Communities tutorial"
description = "Power to the people"
date = 2022-05-26
+++

![Communities in @lnp2pBot](/images/communities.jpg)

Finally the communities are here 🥳 and in this tutorial I will explain how to use it.

## Incentive

Before explaining how it works, I think it is important to explain the community incentive system, since its creation [@LNp2pBot](https://t.me/lnp2pbot) has charged the seller a fee for successfully completed order, this fee is currently 0.6%, now, if the order was created in a community, the bot divides this fee, one part remains for the bot and the other part will be the profit of that community.

Let's explain this in detail with several examples:

Suppose we have created the community **lnp2pBot Madagascar**, the **community fee** is _variable_ and is indicated as a percentage to the bot, so the value must be between 0 and 100, to make it easier to visualize the `max fee` of the bot we will set it to 1%.

#### Example 1:

```
Maximum bot fee: 1% (relative to the amount of the order)
Community fee: 100%
Order with amount = 100000 sats
1% = 1000 sat
On this 1% we will work
70% of 1000 sats will go to the bot = 700
30% of 1000 sats will go to the community = 300

Total fee paid by seller 1000 sats
```

In a community that charges 100% of your fee, the bot will stop charging 100% of your fee and go down to 70%, the remaining 30% is going to the community.

#### Example 2:

```
Max bot fee: 1%
Community fee: 50%
Order with amount = 100000 sats
1% = 1000 sat
70% of 1000 sats will go to the bot = 700
15% (half of 30%) of 1000 sats will go to the community = 150

Total fee paid by seller 850 sats
```

In this example, it is the seller who benefits and could choose to sell in this community since he has to pay a lower fee.

#### Example 3:

```
Max bot fee: 1%
Community fee: 0%
Order with amount = 100000 sats
1% = 1000 sat
70% of 1000 sats will go to the bot = 700
0% of 1000 sats will go to the community = 0

Total fee paid by seller 700 sats
```

In this case, the seller obtains a higher profit.

To counteract what has been explained, I give the same example but without community, that is, any order that the user takes from the global channel @p2pLightning.

#### Example 4:

```
Max bot fee: 1%
Order with amount = 100000 sats
1% = 1000 sat
100% of 1000 sats will go to the bot = 1000

Total fee paid by seller 1000 sats
```

Here the seller pays the full fee to the bot so you probably prefer to find or create a community.

## Creating a community

To create a community we must write or select the command `/community` from the menu, the bot will guide you through the process as I explained in this [post](https://grunch.dev/es/blog/lnp2pbot-communities/) that if you have not read it, I recommend that you do so before continuing this reading, it is important that you know that both you and the bot must be administrators in the community group and in the channels linked to it.

![Creating a community](/images/community-menu.jpg)

## How can I know the communities that work with my currency?

For this we have a new command `/findcomms`, which receives the currency code as a parameter, it can be a standard code **ISO 4217**, but there can also be communities that operate sats against some other cryptocurrency, there is no type of limitation for the user, in the following image we can see the result of `/findcomms usd`.

![Finding communities](/images/findcomms.jpg)

## Eligiendo una comunidad

Now we select the community in which we want to participate and the bot will show us the number of successful orders and the volume traded in the last 24 hours in that community, below we see a button that says **Use by default**, when tapping that button we are already participating in that community.

![Choosing](/images/comm-detail.jpg)

Now we just have to create an order by typing the `/buy` or `/sell` command depending on what we want and follow the bot's instructions.

![Creting an order](/images/sell.jpg)

If we want to leave the community we use the `/setcomm off` command as explained in [Communities in @LNp2pBot](https://grunch.dev/es/blog/lnp2pbot-communities/)

## Managing your community

To maintain a healthy community, the admin must be surrounded by responsible people who help him with the task, this is where the **solvers** come in.

Solvers are simply users who are in charge of resolving disputes and maintaining order in the community, a community must have at least one solver, for this there is no type of requirement, the administrator is the one who designates them when creating the community and can add or remove at any time, an administrator can be a solver in his own community.

The admin can also modify any field of the community at any time, for example you can start a community with a single channel so that users can view purchases and sales, but as the community grows you can change this and use two channels, one to view purchases and one for sales, change the community rate when you deem it necessary.

## Solving disputes

When a user initiates a dispute, a message will be sent by the bot to the dispute channel, only **solvers** will be able to take them by tapping on the **Take dispute** button.

![Dispute](/images/dispute.jpg)

![Dispute detail](/images/dispute-detail.jpg)

Once the **solver** takes the dispute, the bot gave him all the necessary information to resolve the dispute, but he must also communicate with each party, understand what happened and complete the order or cancel it, for this he has two new commands.

### Completing an order

Many disputes start because one of the two parties was slow to respond to a message, but there may also be a seller who wants to keep the fiat money and have the sats returned, for these cases the **solver** can execute the command ` /settleorder`, what this command does is it accepts the payment from the seller and automatically sends the sats to the buyer.

![Completing an order](/images/settleorder.png)

### Canceling an order

The **solver** can cancel the order which returns the sats to the seller if it considers it correct, for this it must execute the `/cancelorder` command as we can see in the following image.

![Canceling an order](/images/cancelorder.png)

### Eliminating disputes

Every time a user initiates a dispute, both the person who initiated the dispute and the counterparty are involved in it, until we know what happened, the two are related by a dispute, by resolving the dispute the solver can eliminate this dispute to one of the users or even both if is necessary, this can be done with `/deldispute username order-id`.

![Eliminating disputes](/images/deldispute.png)

## Ban a user from the community

If a solver deems it necessary, they can ban a user from the community with the command `/ban` followed by their username.

**_IMPORTANT:_** To execute the community administration commands you always have to have selected **by default** the community with which you want to work, you can do it by tapping the button **Use by default** that is shown after executing `/findcomms`, you can also do it with the `/setcomm @group` command, a telegram group gets their **@group** only when they are public.

## Withdrawal of earnings

In the coming weeks the administrators will be able to withdraw the profits from their communities, the more orders are processed in their community, the greater the amount of sats to be withdrawn, stay tuned that at any time you will see the option.

## Conclusions and thanks

For a long time I have been thinking about how to make the bot scale, I told many people waiting for a **ahá moment**, until the time came, the idea of ​​communities I heard for the first time talking to my friend [Ximena](https://twitter.com/XTezanosP) who told me about trust and based on her ideas I began to build what we have today, another person who his ideas have helped me in the development of communities is my friend [Manu](https://twitter.com/manuferraritano).

Many more people have collaborated with the project, each in their own way, thank you all 🤖.

The communities in @lnp2pbot are evolving fast, the same way the bot evolved its early days, but I am not interested in a fast adoption, I consider organic growth much healthier and it seems that this will be the trend with the use of communities, We look forward to your feedback on [github](https://github.com/grunch/p2plnbot/issues) or in our [telegram](https://t.me/lnp2pbotHelp) group.
