
# Reputation System for crypto.camp 

## Preface
hack.ether.camp is a platform which held a virtual accelerator during November and December 2016. Every person was able to support projects with both *Kudos* and *Hacker Gold (HKG)*. *Kudos* were a voting power of a system participant. Any registered person had 10 *Kudos* and they could have up to 210 *Kudos* after linking 2 active social accounts.
It was a system with minimal constraints, but vulnerable to sybil attacks as long as to massive voting by people who registered just before giving the vote. One of the top camps received more than 87% of the overall votes from newcomers. It means that a lot of voters don't want to choose the best project carefully. They don't want to compare and to produce some value for the community by giving fair reviews and some constructive critics.
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
Reputational system can be presented as a graph where nodes are actors of the system and edges are relationships between the actors. Reputational function is called symmetric if reputation depends only on interactions between actors, not on actor's roles and statuses. And asymetric reputational fuction depends both on graph edges weights and on nodes labels.

* TODO some pictures of graphs here? *

All symmetric reputation systems are proven to be sybil-vulnerable. One of the famous approaches to build a symmetric reputational system was [*PageRank*](https://github.com/ether-camp/public/blob/master/reputation.md#1-the-pagerank-citation-ranking-bringing-order-to-the-web-httpilpubsstanfordedu809042211999-66pdf) which was vulnerable to creating sybil entities such as *doorways*. So we need to make a sybil creation unaffordably expensive or use some sybil-protection mechanisms.

## Prisoners dilemma and transitive trust systems
We can consider the reputation growing as a *game* where every user chooses if he is going to rate the content honestly or not. The classical problem of the game theory is [*Prisoners dilemma*](https://en.wikipedia.org/wiki/Prisoner's_dilemma) where each actor chooses whether to cooperate or to defect. 

*todo matrix form of the game*

T > R > P > S

The *dominant strategy* for this game is to *defect*, because it provides the maximum payoff for every player who chooses it. Applying to our case it means that every voter will have rational tendency to rate content unhonestly to maximize his payoff.
In the case if this game is repeated in N rounds and all actors have memory about prior actions of other participants the *superrational* players can choose another strategy.
So called Grim strategy is to cooperate with other players unless they defect in previous round. Every superrational player gets a better payoff, because he receiving R in the majority of rounds.

To make this game sustainable we need at least 2 things:
- Some metrics of the player behaviour. How often he chooses to defect.
- The number of rounds should be big.

Hack.ether.camp is meant to be a long-term virtual accelerator, so the reputation score can be valuable during the long period and solves the second problem.
Let's describe the *karma* as the main metric of player's cooperating behaviour.

## Karma gain system
We are creating asymetric reputational function. Therefore, we need to designate some actors subset as trustful and give them rights to rate activity of other actors and reward valuable community members with trustful status.
Every participant of the system can create a content which can be rated by users with high enough karma. When an author of the comment or post receives some upvotes for his content, he receives karma depending on formula:

`received_karma == (voter_karma) / karma_coefficient`

**karma_coefficient** can be changed in future and equals **25** on the start.

### Actors roles

Every actor has a role depending on his karma amount. The role is also determines maximal amount of karma which can be received by actor for one day.
The roles are:

```
- Newcomer, karma 0-99, max gain daily 20.
- Voter, karma 100-5000, can vote for the projects and content, max gain daily 100.
- Elder, karma >5000, can also have some moderation rights.
```

Every normal person starts with 0 karma. It should be quite hard to get a voter status, because newcomers cannot harm the system badly. They only can create a number of spam posts which can be easily hidden by other users if they like. 

### Karma growing curve
It easily can be seen that even if user posts good content and receives upvotes every day his overall karma gain will be low when his rank is low. It's always more reasonable to develop user's main account instead of sybils after he overcome the first stage, the newcomer status.

![Karma growing curve](http://imgur.com/oxIl6dn.png)

### Upvotes limits
Every should be done carefully and meaningfully, therefore it should be a daily depleatable resource which also can depend on user's karma.
```
daily_upvotes_limit = voter_karma / 20
```
According to this formula, user who nearly got karma == 100 and *voter* status will be able to give 5 upvotes a day.

## Known attacks

There is a number of known attacks on reputational systems. For example, every user easily can change his displayable identity, create a number of sybils or give feedback not honestly. 

### Whitewashing attack
*Whitewashing* - An entity may acquire a new user and start over with the new reputation list.

#### Preventing whitewashing


### Sybils attack
Every user can create additional entities or hire some users who are not interested in platform development and creating quality content.

#### Rebuffing sybils

### Dishonest feedback
Every user can post content and receive additional karma for it. Also, he has voting power which can be spread over the posts of other users, because he cannot vote for himself. If there is no reward for honest upvoting good content his dominant strategy will be slowly upvoting sybils.
Considering a constraint of minimum 2 upvotes for some post to get karma, maleficent *voter* can create another *voter* as sybil or unite with someone to start more sybils growing.

```
        Karma
Voter 1 100
Voter 2 100
```

```
         Day 1 ... Day 13
Sybil 1      8 ... 104
Sybil 2      8 ... 104
```

So, on the 13th day there will be a number of sybils even though there was no karma growth of maleficient users and they were only 2 of them. 

User have to have some inception to give feedback more honestly. Let's propose reward system for a fair voting.

#### Rewards for the votes
We need to choose a metric of a good upvote. In a decentralized environment we cannot use some rating center which will define  how the good content should look like. The popularity can be a measure of value of the content for the community. Let's choose *popularity* as main metric of content quality. If actor chooses to upvote some unrated content which is lately upvoted by more user he should be rewarded for it.

The rules for the rewarding are:
- There should be minimal quantity of users who upvoted content to consider it popular
- First upvoter receives maximal reward, reward will decrease for every subsequent upvoter
- Upvoting already popular content will not reward you with additional karma

Initial constants are:
- popular_content_upvotes >= 10
- first_upvoter_reward = 10 karma
- max_rewarded_users = 10
- reward_decrease_step = 1

So, first upvoter of the popular content with 10 or more upvotes receives 10 karma, 10th upvoter gets 1 karma.

### Initial karma distribution and project reviews
In order to create an initial kernel of trustful users we can give some karma to actors at our discretion. Along with this or istead of it we can delegate initial karma granting function to the owners of the projects.
Project reviews supposed to be a mechanism which will help to accumulate initial karma value by the community new members. Every user can write a review for any published project. We propose to give the project an option to recognize the review. Review recognition doesn't say that the project agrees with it but it gives some mechanism to encourage reviewers to be constructive in their critics. Any owner of this project can *recognize* the review and make it visible to other community members.
First review for the particular project can give more karma. It will stimulate to give the reviews for all projects.

(*TODO should the review be hidden before the approve or should there be another tab for unapproved reviews?*). 

An author of the review receives *50 karma* in the case of *recognition*. All the reviews can recieve normal votes from the community after the approval like any other post or comment. First recognized review for the project also receives $60 in HKG equivalent to reward reviewing every project.

#### Camp owners identity verification
If any user of the platform can start a camp, therefore there could be fake camps which can be used to take advantage in acquiring the karma.
Hack.ether.camp is going to be a verification center, which will be able to check if camp and camp owners are real people and they can get the rights to recognize the reviews.

### Creating a sybil-proof system
Main problem here: any voter can decide to start growing an army of sybils, because he has a right to upvote. He cannot vote for himself, but he needs the reasons to give votes fairly and not for his sybils. There are some ways to prevent sybil attacks in this scheme. 

#### Provably sybil-proof systems
As been shown in [Algoritmic game theory](https://github.com/ether-camp/public/blob/master/reputation.md#2-algorithmic-game-theory-nisan-noam-edt-roughgarden-tim-edt-tardos-eva-edt-vazirani-vijay-v-edt-690-693), both *PageRank* and *max-flow* ranking systems are vulnerable to sybil attacks.

*TODO: proof of it?*

*PathRank* is an approach to reputation calculation based on the shortest path. Here is a graph where weight of the edge is a inverted amount of given *karma*.

*TODO: picture of graph *

So, as it easily can be seen, adding more nodes with low karma and edges to the node which karma is desired to be improved cannot change anything, because shorter path will always use edges from high-rank actors. This system is sybil-proof, but it has another flaw. The votes of the new members cannot impact some user's karma if there were votes from high-rank actors. It becames some kind of *aristocrathy* in the meaning of form of government. And we are going to build a democratic system. So PathRank is not an option and we have to use ... *TODO!!!*

#### Slowing down sybils growing
Growing of sybils can be made too slow by additional constraints, which can be:
- An actor have to get at least 2 upvotes from different users to get a karma for your content. 
- There can be activity coefficient which will reward constantly high activity, like +2% to karma if there were upvoted content in the last days, up to 10%, or decrease up to 10% if there was no activity in last 5 days.
- After 3 days in a row of upvoting specific person by another specific person there should be cooldown for 1 or 2 days. So one user cannot be upvoted too much by single voter or even elder. 

( *todo: to consider if the 3 days restriction wont harm the constructive behaiviour* )

Another way to prevent such attack is rewarding upvotes for the good content. If one actor voted up some content which was upvoted therefore by, for example, 10 or more users, he receives also some amount of karma along with content author. 

Proof of identity by linking the social accounts, which were used in hack.ether.camp season 2, also can be useful if it's impact is not too big. Social accont link can be some prove of your identity, so it will give you *+5%* to karma grow per each linked account with more than 100 followers.

Some additional options to consider: 
- Karma growth can be boosted during the first weeks or months after the system launch to help the community grow more intensively.
- Hide button. If voter considers that some content is spam or offensive, he can press 'Hide' button, so he will not see this comment or post in the future. After getting *(voter_karma) / 25 == 100* the author of content gets a cooldown for karma acquiring for 1 day. (Effect can be worse if he will get more 'spam' flags).

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
As been shown above, it's always more profitable to use deposit as a boost for your main account, except the case when some maleficient user already has a control over at least 2 accounts with *voter* status and he doesn't wants to produce a content anymore. 
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

## Moderation
(*TODO!!!*)
### Elders, shamans and chief

Shamans can punish with a lowing coefficient on the few nexts posts or bless with additional labels for the post, chief can decline shamans offers. 
(*TODO: Can chief punish and reward by himself?*)

### Turnover of power
Every week 10 shamans and 1 chief randomly choosen among the elders. If there are not enough elders, number of shamans will be lower.

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

All contract data is open, so any user is able to analyze the interactions between actors. External social graph analyzing tool can be a good help for an ordinary user who wants to see if there are exist evidences of unfair interaction. E.g. user can see if there are sybil clusters and who is a owner of this cluster. None of this tools can be a automated judge which changes some user karma, but it may advise to do so. 

(*TODO what additional tools we can propose?*)


## References
##### [1] The PageRank Citation Ranking: Bringing Order to the Web. http://ilpubs.stanford.edu:8090/422/1/1999-66.pdf
##### [2] Algorithmic Game Theory. Nisan, Noam (EDT)/ Roughgarden, Tim (EDT)/ Tardos, Eva (EDT)/ Vazirani, Vijay V. (EDT). 690-693

===

## Points to think about 

- [ ] Projects may invite reviewers setting bounty pay for each review 
- [ ] Under the review specipy users that upvoted and are using deposits - (find name maybe shamans )  
- [ ] Consider user activity as additional param with karma gain.
