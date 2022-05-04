+++
title = "Communities in @LNp2pBot"
description = "Giving more power to users"
date = 2022-04-29
+++
![Communities in @lnp2pBot](/images/communities.jpg)

Since we started this project we wanted to start operating with [@LNp2pBot](https://t.me/lnp2pbot) without making much noise, it was essential to test the bot in a small community before showing the world a final product, that's why We started working with the Spanish-speaking lightning community, which received the project in a very good way. The feedback received has allowed the bot to grow and improve day by day.

## Displaying user orders
As the project started with a small group in which everyone shared the same language, a channel to view the orders was not a problem, so we started solving priority problems by letting the users themselves be the gauge of what was needed and when. develop it, but the time has come when the number of published orders is very large, which has led many users to ask themselves the same question.

## How can I filter the orders by my currency?
One of the most recurring questions from users is how can I filter orders by my currency? There are several ways to solve this, there is always the temptation to do it the easy way and ask the developer to create a new channel and then redirect all orders of a specific currency to the new channel, but what seems easy at first glance leads to other problems.

By creating currency channels centrally, the `admin` must take care of the needs of users in the most remote places on the planet, there are 175 fiat currencies globally, some countries share currencies, so potentially at some point it would be necessary to create manually more than 175 channels, but the problem remains that users must ask for permission to use their currency in a specific channel using the bot, **@lnp2pBot** faithfully follows the Bitcoin philosophy and although it is linked to telegram we seek to be "`permissionless`", without custody and above all **without KYC**.

## Communities
This is where the communities come in, now users have much more freedom of maneuver, from now on they will be able to create and manage their own communities to buy and sell sats, limiting these operations to the fiat currencies used in the region in an autonomous way, but... *with great power comes great responsibility*, this means from the moment users start working in their own communities they will also have to manage the possible disputes that occur.

## Trust model
The model we have chosen to resolve disputes is that of trust, this seems paradoxical for those of us who work with Bitcoin because we have always advocated "`trustless`" systems, and the truth is that the goal is always to develop systems where we do not have to trust anyone, but after thinking a lot about dispute resolution systems, we had to accept that you always have to trust someone for it.

## Benevolent Dictator
Having accepted that to resolve a dispute you have to trust someone, our focus shifted to, how do we make there less incentive to create disputes? in this the community made several proposals and the one we are developing because at the time of writing this text it is not ready yet, is to use the figure of the benevolent dictator, this is a dictator that people accept and trust that their decisions are made for the good of the community.

The benevolent dictator is the creator of the community, the benevolent dictator is the one who can add dispute solvers, these `solvers` are publicly known by the community, by this I mean their telegram username, and the community will take care of denouncing if these users are doing their work correctly or not, in a community where the benevolent dictator correctly selects the solvers everything will work fine, in one where the dictator makes mistakes his `subjects` will go to another community or create a new one, in this way we delegate this responsibility to the communities.

## How do I create a community?
To create a community simply write the command `/community`, after this the bot will ask you to indicate the following:

* **Community Name**: A name to identify your community.
* **Currencies**: Fiat currencies that can operate in the community, these must be entered separately from a blank space, for example for a Uruguayan community "UYU USD" can be added.
* **Community group**: This is the main group where community members meet, in this group both @lnp2pbot and the one who is creating the community must be administrators, users can create orders by sending bot commands in this group.
* **Channel or channels order book**: The orders will be published where we indicate to the bot, if we enter a single channel, the purchases and sales will be published in that channel, but if you indicate two channels, the purchases will be will publish in the first and sales in the second, the channels are entered separated with a blank space, both the bot and the creator of the community must be administrators of the channels.
* **Solvers**: We must indicate the usernames of the users who will be in charge of resolving the disputes, separated by a blank space.
* **Channel for disputes**: In this channel the bot will publish when a user initiates a dispute, both the bot and the creator of the community must be administrators of the channel.

![Creating a community](/images/community01.jpg)

![Creating a community](/images/community02.jpg)

To modify any field we simply execute the `/mycomms` command and the bot will show you a menu that will help you select the community you want to modify and the specific field.

![Modifying a community](/images/mycomms.jpg)

## Creating orders
The operation of the bot remains exactly the same, by default it creates the orders in a global channel, but since we have created a new community we want our order to be published in the channel that we have associated with the community, there are two ways to create an order in the new community.

1. We enter the community group, in the case of our example it would be `@p2pZimbabwe` and within the group we execute the usual command, `/sell` or `/buy`.
2. If we want something more private, we indicate our default community to the bot by executing the command `/setcomm @p2pZimbabwe`, from that moment all the orders that you create in private will go to the corresponding channel linked with `@p2pZimbabwe`, you can change your default community whenever you want with `/setcomm @MuchMoreCoolCommunity` or go back to the previous state, where you didn't have a default community by running `/setcomm off`.

## What's missing?
Most of it is done, but it's not finished yet, the dispute sub-system is still to be finished, a command to show the communities for coins to the users is missing, and probably many other things are missing that I don't have in mind yet but they will be genuine user requirements, all this will be ready in the next few weeks.

## Conclusions
The communities in @lnp2pbot is in experimental mode, so we hope that there will be bugs to fix, but as it has happened since the beginning we know that we will receive the appropriate feedback from our users to improve what we have developed so far, the next step is that the users create their own communities and everyone participates in the community(s) that suits them best, we await your feedback on [github](https://github.com/grunch/p2plnbot/issues) or in our group of [telegram](https://t.me/lnp2pbotHelpEn).