
# Reputation System for crypto.camp 

- [Reputation System for crypto.camp](#)
	- [Preface](https://github.com/ether-camp/public/blob/master/reputation.md#preface)
	- [Terms](https://github.com/ether-camp/public/blob/master/reputation.md#terms)
	- [Motivation](https://github.com/ether-camp/public/blob/master/reputation.md#motivation)
	- [Prisoners dilemma and transitive trust systems](https://github.com/ether-camp/public/blob/master/reputation.md#prisoners-dilemma-and-transitive-trust-systems)
	- [Karma gain system](https://github.com/ether-camp/public/blob/master/reputation.md#karma-gain-system)
	- [Known attacks](https://github.com/ether-camp/public/blob/master/reputation.md#known-attacks)
	- [Reputation system usecases](https://github.com/ether-camp/public/blob/master/reputation.md#reputation-system-usecases)
	- [Moderation](https://github.com/ether-camp/public/blob/master/reputation.md#moderation)
	- [Kudos as a measure of project's quality](https://github.com/ether-camp/public/blob/master/reputation.md#kudos-as-a-measure-of-projects-quality)
	- [Who pays for the gas](https://github.com/ether-camp/public/blob/master/reputation.md#who-pays-for-the-gas)
	- [Notes on technical implementation](https://github.com/ether-camp/public/blob/master/reputation.md#notes-on-technical-implementation)
	- [External tools](https://github.com/ether-camp/public/blob/master/reputation.md#external-tools)
	- [References](https://github.com/ether-camp/public/blob/master/reputation.md#references)

![](http://i.imgur.com/b2F0h8j.png )  |  ![](http://i.imgur.com/d1ZYTW6.png) |  ![](http://i.imgur.com/zXJp4PP.png ) |  ![](http://i.imgur.com/wnULjFQ.png ) |  ![](http://i.imgur.com/tfQqazf.png )





## Preface
hack.ether.camp is a platform which held a virtual accelerator during November and December 2016. Every person was able to support projects with both *Kudos* and *Hacker Gold (HKG)*. *Kudos* were a voting power of a system participant. Any registered person had 10 *Kudos* and they could have up to 210 *Kudos* after linking 2 active social accounts.
It was a system with minimal constraints, but vulnerable to sybil attacks from massive voting by people who registered just before giving the vote. One of the top camps received more than 87% of the overall votes from newcomers. It means that a lot of voters don't want to choose the best project carefully. They didn't want to compare and produce value for the community by giving fair reviews and some constructive critics.
We, as authors of the platform, want to change it. Loudest projects are not always the best. So, let's the innovative ideas and implementations win.

## Terms
- Karma - points attached to some user identity to depict his reputation in the system
- Kudos - voting power of user. Can be calculated as a linear function of *karma*
- User - anybody who is logged in to the system
- Sybil - additional account of some user which can be used to enhance his voting power
- Project (camp) - idea is the essence of innovation, the idea that seek to meet the reality is a project
- Vote - transaction where user gives some of his available kudos to the selected project

## Motivation 
The main goal of system is to create a community of experts which votes for the projects or any other content is trustful. 
Reputational system can be presented as a graph where nodes are actors of the system and edges are relationships between the actors. The reputational function is symmetric, if reputation depends only on interactions between actors, not on actor's roles and statuses. The asymetric reputational fuction depends both on graph edges weights and on nodes labels.
On the picture below there is an example of both symmetric and asymetric reputational functions. As can be seen, switching labels on the graph describing asymetric reputational function will change the weight of the edges.

![Reputational functions](http://imgur.com/rQi7KeZ.png)

All symmetric reputation systems are proven to be [sybil-vulnerable](https://github.com/ether-camp/public/blob/master/reputation.md#provably-sybil-proof-systems). One of the famous approaches to build a symmetric reputational system was [*PageRank*](https://github.com/ether-camp/public/blob/master/reputation.md#1-the-pagerank-citation-ranking-bringing-order-to-the-web-httpilpubsstanfordedu809042211999-66pdf) which was vulnerable to creating sybil entities such as *doorways*. So we need to make a sybil creation unaffordably expensive or use some sybil-protection mechanisms.

## Prisoners dilemma and transitive trust systems
We can consider the reputation growing as a *game* where every user chooses if he is going to rate the content honestly or not. The classical problem of the game theory is [*Prisoners dilemma*](https://en.wikipedia.org/wiki/Prisoner's_dilemma) where each actor chooses whether to cooperate or to defect. 


Strategy        | Cooperate	| Defect
----------------|-----------|-------
Cooperate | R, R | S, T
Defect | T, S | P, P

Where the condition T > R > P > S must hold for the payoffs, and are defined as follows

P: Punishment payoff
R: Cooperation reward
S: Suckers payoff
T: Tempatation payoff



The *dominant strategy* for this game is to *defect*, because it provides the maximum payoff for every player who chooses it. Applying to our case it means that every voter will have rational tendency to rate content dishonestly to maximize his payoff.
In the case if this game is repeated in N rounds and all actors have memory about prior actions of other participants the [*superrational*](https://en.wikipedia.org/wiki/Superrationality) players can choose another strategy.
So called Grim strategy is to cooperate with other players unless they defect in previous round. It easily can be shown that every *superrational* player gets a better payoff, because he is receiving R in the majority of rounds.

To make this game sustainable we need at least 2 things:
- Some metrics of the player behaviour. How often he chooses to defect.
- The number of rounds should be big.

Hack.ether.camp is meant to be a long-term virtual accelerator, so the reputation score can be valuable during the long period and solves the second problem.
Let's describe the *karma* as the main metric of player's cooperating behaviour.

## Karma gain system
We are creating asymetric reputational function. Therefore, we need to designate some actors subset as trustful and give them rights to rate activity of other actors and reward valuable community members with trustful status.
Every participant of the system can create a content which can be rated by users with high enough karma. When an author of the comment or post receives some upvotes for his content, he receives karma depending on formula:

`received_karma = (voter_karma) / karma_coefficient`

**karma_coefficient** can be changed in future and equals **25** on the start.

### Actors roles

Every actor has a role depending on his karma amount. The role is also determines maximal amount of karma which can be received by actor for one day.
The roles are:

```
- Newcomer, karma 0-99, max gain daily 20.
- Voter, karma 100-5000, can vote for the projects and content, max gain daily 100.
- Elder, karma >5000, can also have some moderation rights, max gain daily 300.
```

Every normal person starts with 0 karma. It should be quite hard to get a voter status, because newcomers cannot harm the system badly. They only can create a number of spam posts which can be easily hidden by other users if they like. 

### Karma growing curve
It easily can be seen that even if user posts good content and receives upvotes every day his overall karma gain will be low when his rank is low. It's always more reasonable to develop user's main account instead of sybils after he overcome the first stage, the newcomer status.

![Karma growing curve](http://imgur.com/oxIl6dn.png)

### Initial karma distribution and project reviews
In order to create an initial kernel of trustful users we can give some karma to actors at our discretion. Along with this or istead of it we can delegate initial karma granting function to the owners of the projects.
Project reviews supposed to be a mechanism which will help to accumulate initial karma value by the community new members. Every user can write a review for any published project. We propose to give the project an option to recognize the review. Review recognition doesn't say that the project agrees with it but it gives some mechanism to encourage reviewers to be constructive in their critics. Any owner of this project can *recognize* the review and make it visible to other community members.
First review for the particular project can give more karma. It will stimulate to give the reviews for all projects.

An author of the review receives *50 karma* in the case of *recognition*. All the reviews can receive normal votes from the community after the approval like any other post or comment. First recognized review for the project also receives $60 in HKG equivalent to reward reviewing every project.

#### Camp owners identity verification
If any user of the platform can start a camp, therefore there could be fake camps which can be used to take advantage in acquiring the karma.
Hack.ether.camp is going to be a verification center, which will be able to check if camp and camp owners are real people and they can get the rights to recognize the reviews.

## Known attacks
There is a number of known attacks on reputational systems. For example, every user easily can change his displayable identity, create a number of sybils or give feedback not honestly. 

### Whitewashing attack
*Whitewashing* - An entity may acquire a new user and start over with the new reputation list.

#### Preventing whitewashing
In any online reputational system whitewashing will always be a problem. One of the approaches to prevent it is to require some proof of identity like verified phone number or social account. We are going to build a reputational system for a cryptocurrency community where anonymity is always highly preferable. Also, as it was seen from the second season of *hack.ether.camp* such identities can be easily bought. We still can use a social account proof of identity as an additional way to show user as more trustful, but it can't be the main way.
Another approach to prevent whitewashing and to force users to stay on their primary account is to make the reputation valuable and hard to acquire. As been shown above in [Transitive trust systems](https://github.com/ether-camp/public/blob/master/reputation.md#prisoners-dilemma-and-transitive-trust-systems) section, users will tend to consider their reputation valuable in a long-term environment. Also, [Karma growing curve](https://github.com/ether-camp/public/blob/master/reputation.md#karma-growing-curve) section describes why it's always harder to grow a new account instead of preserving already developed one.
The last thing here is the point that every person who gains significant karma has usually creating valuable content in the past. Even if at some point some user chooses to defect he already produced some value for the community and the harm of his maleficient behavior can be compensated by his content and honest ratings.

### Sybils attack
Every user can create additional entities or hire some users who are not interested in platform development and creating quality content.

### Creating a sybil-proof system
The main challenge here is that any voter can decide to start growing an army of sybils, because he has a right to upvote. He cannot vote for himself, but he needs the reasons to give votes fairly and not for his sybils. There are some ways to prevent sybil attacks in this scheme. 

#### Provably sybil-proof systems
As been shown in [Algoritmic game theory](https://github.com/ether-camp/public/blob/master/reputation.md#2-algorithmic-game-theory-nisan-noam-edt-roughgarden-tim-edt-tardos-eva-edt-vazirani-vijay-v-edt-690-693), *PageRank*-like reputational functions are vulnerable to sybil attacks.

**Proof:**
Let G=(V,E) is a graph where nodes are reputational system actors and the weight of the edge between nodes *a* and *b* are cumulative *karma* value which was given by user *a* to user *b*. Then, *a* decides to create a sybil node *s* and increase it's *karma* enough to make a vote. After that, *s* upvotes *a*, so sybil attack can easily be performed, QED.

![Sybil attack on PageRank](http://imgur.com/awCxPkL.png)

*PathRank* is an approach to reputation calculation based on the shortest path. There is a *R* node which is root of the graph. The weight of the edges is an inverted amount of given *karma*. Therefore, we can find total karma value for every node by calculating shortest path between the root and certain node.
As shown on the picture below, creating additional sybil node cannot affect the shortest path to the maleficient node, because the shorter path will always use edges from more high-ranked actors. *PathRank* can be considered sybil-proof.

![Shortest path reputational system](http://imgur.com/GwvdGJB.png)

However, such reputational function has another flaw. The votes of the new members cannot impact some user's karma if there were votes from high-rank actors. It becames some kind of *aristocrathy* much like a form of government. It doesn't fits the requirements to build a distributed and democratic system reputation system. So PathRank is not an option. Instead of it we can use an approach which is close to the *PageRank* with some additional constraints to make the sybil attack more problematic for the attacker.

#### Slowing down sybils growth
Growth of sybils can be slowed by additional constraints, which can be:
- An actor have to get at least 2 upvotes from different users to get a karma for your content. 
- There can be activity coefficient which will reward constantly high activity, like +2% to karma if there was upvoted content in the last days, up to 10%.
- After 3 days in a row of upvoting specific person by another specific person there should be cooldown period for 1 or 2 days. So one user cannot be upvoted too much by single voter or even elder. 

( *todo: consider if the 3 days restriction will harm the constructive behaiviour* )

Another way to prevent such attack is rewarding upvotes for the good content. If one actor voted up some content which was upvoted therefore by, for example, 10 or more users, he also receives some karma along with the content author. 

Proof of identity by linking the social accounts, which were used in hack.ether.camp season 2, also can be useful if it's impact is not too big. Social accont link can be some prove of your identity, so it will give you *+5%* to karma grow per each linked account with more than 100 followers.

Some additional options to consider: 
- Karma growth can be boosted during the first weeks or months following system launch to help grow the community more intensively.
- Hide button. If voter considers that some content is spam or offensive, he can press 'Hide' button, so he will not see this comment or post in the future. After getting *(voter_karma) / 25 == 100* the author of content gets a cooldown for karma acquiring for 1 day. (Effect can be worse if he will get more 'spam' flags).


### Dishonest feedback
Every user can post content and receive additional karma for it. Also, they have voting power which can be spread over the posts of other users, because they cannot vote for themselves. If there is no reward for honest upvoting of good content, a users dominant strategy could be gradual upvoting of sybils.
Considering a constraint of minimum 2 upvotes for some post to get karma, a maleficent *voter* can create another *voter* as sybil or unite with someone else to get more sybils growing.

Voters | Karma
-------|------
Voter 1 | 100
Voter 2 | 100

Sybils / Karma | Day 1 | Day 2 | Day 13
---------------|-------|-------|-------
Sybil  | 8 | 16 | 104

So, on the 13th day there will be a number of sybils even though there was no karma growth of maleficient users and they were only 2 of them. 

User have to have some incentive to give feedback more honestly. Let's propose a reward system for the fair voting.

### Rewards for the votes
We need to choose a metric of a good upvote. In a decentralized environment we cannot use a rating center that will define how the good content should be. The popularity can be a measure of value of the content for the community. Let's choose *popularity* as main metric of content quality. If actor chooses to upvote some unrated content which has been recently upvoted by more user he should be rewarded for it.

The rules for the rewards are:
- There should be minimal quantity of users who upvoted content to consider it popular
- The first upvoter receives the maximum reward, the reward will decrease for every subsequent upvoter
- Upvoting already popular content will not reward you with additional karma

Initial constants are:
```
- popular_content_upvotes >= 10
- first_upvoter_reward = 10 karma
- max_rewarded_users = 10
- reward_decrease_step = 1
```

So, first upvoter of the popular content with 10 or more upvotes receives 10 karma, 10th upvoter gets 1 karma.

### Upvotes limits
Every upvote should be done carefully and meaningfully, therefore it should be a daily depleatable resource which also can depend on user's karma.
```
daily_upvotes_limit = voter_karma / 20
```
According to this formula, user who nearly got karma == 100 and *voter* status will be able to give 5 upvotes a day.

### HKG deposit

Any person who wants to invest in platform development and show himself as a long-term supporter can make a deposit. Main currency of hack.ether.camp is *Hacker Gold (HKG)*. Also, deposit can be another way to prove user's identity and to prevent sybils entities creation.
As a reward those who invested in a platform can receive additional karma. 
Following to this link https://etherscan.io/token/HackerGold#balances, only 123 of 506 HKG holders have more than 10k HKG. The majority of holders have 1k or less. So, the proposal is to set minumal deposit amount which can give you some additional power to 1000 HKG.

In the case if user has such deposit, he will get a karma boost x3 for all the received upvotes.

The formula is

`karma +=  karma_gain * 3^a`

while the *a* depends on HKG you deposit:

- < 1000HKG => a = 0
- 1000HKG => a = 1
- 5000HKG => a = 2
- 10000HKG => a = 3

The deposit doesn't changes user's daily karma limits, but it helps him to pass the current level in the hierarchy more quickly.

*Example:*
User have 2 supporters with karma 100 and gets upvotes every day, 5 days in a row.

`daily_karma_gain = (100 / 25) * 2 * 3 = 24 (actually, 20 due to daily limit)`

Therefore, user still have to produce valuable content to get his karma, but if someone want your voting rights quickly, he can achieve it with deposit. Moreover, when this user gets next status, the daily limit stops to affect him, so it makes sense to hold the deposit on the main account, not on the sybils.

#### Deposit activation and withdrawal
Hack.ether.camp doesn't wants to take these deposits once and forever. It can be easily withdrawed at any moment. But to prevent manipulations with different accounts, every deposit value should be on a particular account at least for *48 hours* to activate it's functions.

#### Attacks using deposit
As been shown above, it's always more profitable to use deposit as a boost for your main account, except the case when some maleficient user already has a control over at least 2 accounts with *voter* status and he doesn't want to produce content anymore. 
If the user with deposit tries to get the rewards for good voting, he will receive for the upvote in the case where he is the first upvoter:
```
1000 HKG deposit => (2 voters) * (10 karma) * 3 == 60
5000 HKG deposit => (2 voters) * (10 karma) * 9 == 180
```

And if the user tries to withdraw his deposit and then put it to the sybil's account he will receive maximum:
```
1000 HKG => (2 voters) * (4 karma) * 3 == 24 (20 due to limit)
5000 HKG => (2 voters) * (4 karma) * 9 == 56 (20 due to limit)
```

It easily can be seen that dominant strategy here is to activily participate in the content rating from the user's main account.

## Reputation system usecases

As been described before, three main ways to get the karma are:
- Get upvotes for the posted content
- Rate the comment and be among the first 10 upvoters
- Write the reviews on the project and get recognition from the project owners

Let's summarize how much karma can be received in the different cases. 

### Project reviews and content creation karma
Receiver | Recogninized project review | Upvote, voter_karma = 100 | Upvote, voter_karma = 400 | Upvote, voter_karma = 5000
---------|-----------------------------|---------------------------|---------------------------|---------------------------
Newcomer | 20 | 4 | 16 | 20
Voter | 50 | 4 | 16 | 100
Elder | 50 | 4 | 16 | 200
Newcomer, deposit 1000 HKG | 20 | 12 | 20 | 20
Voter, deposit 1000 HKG | 100 | 12 | 48 | 100
Elder, deposit 1000 HKG | 150 | 12 | 48 | 300

### Content rating karma
Receiver | Upvoted post got < 10 upvotes | Upvoted post got >= 10 upvotes
---------|-------------------------------|-------------------------------
1st upvoter | 0 | 10
2nd upvoter | 0 | 9
3rd upvoter | 0 | 8
9th upvoter | 0 | 2
10th upvoter | 0 | 1

## Moderation
The proposed reputation system is not absolutely attack-proof, thus it requires additional regulation mechanisms to adjust user's karma values if the community decides that he can be considered maleficient. These moderation functions can be performed only by trusted users with a lot of karma, *the elders*. Moreover, to prevent the situation when of the elders became maleficient or there exists some clique of the elders who decided to *defect* at some point we need to choose the moderators randomly and rotate them periodically.
Also, to control the moderation process itself we can propose a metamoderator role, which will be able to decline the moderators actions due to some cases.

### Elders, shamans and chiefs
Shamans can punish those who choose to defect with a lowering coefficient on the few nexts posts or bless with additional labels for the post, chief can decline shamans offers. 

### Turnover of power
Every week 10 shamans and 1 chief are randomly choosen among the elders. If there are not enough elders, number of shamans will be lower.

## Kudos as a measure of project's quality
Every voter which proved his trustful status can vote also for the projects (camps). They have amount of **kudos** which is linear function of karma:
`kudos = karma / 10`

User can give any available kudos amount to the selected projects when the voting is open.
Karma >5000 cannot affect actor's available kudos, it's only applyable to content moderation.

## Who pays for the gas
Voters are gas payers. When user becames voter he receives some Ether to cast, for example, 10 upvotes. Good content should be rewarded. For every 10 upvotes user receives, for example, 1 HKG to his address. He can exchange it to Ether as some external exchange and use to pay for the gas again.

## Notes on technical implementation
Speaking of implementation of this system it looks that best option will be pluggable system. All the karma and kudos gain coefficients should be stored in separate structures. System should be changeable by submitting proposals which can be accepted or declined by the community.

( *todo: the params for all karma related questions can be changed once a time frame let's say each 200k blocks* )

## External tools

All contract data is open, so any user is able to analyze the interactions between actors. External social graph analyzing tool can be useful for an ordinary user who wants to see if there exist evidence of unfair interactions. E.g. user can see if there are sybil clusters and who is an owner of this cluster. None of this tools can be an automated judge which changes some user karma, but it may advise to do so. 

(*todo what additional tools we can propose?*)


## References
##### [1] The PageRank Citation Ranking: Bringing Order to the Web. http://ilpubs.stanford.edu:8090/422/1/1999-66.pdf
##### [2] Algorithmic Game Theory. Nisan, Noam (EDT)/ Roughgarden, Tim (EDT)/ Tardos, Eva (EDT)/ Vazirani, Vijay V. (EDT). 690-693

===

## Points to think about 

- [ ] Projects may invite reviewers setting bounty pay for each review 
- [ ] Under the review specipy users that upvoted and are using deposits - (find name maybe shamans )  
- [ ] Consider user activity as additional param with karma gain.
