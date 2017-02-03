
# Reputation System for crypto.camp 

#### Motivation 
The main goal of system is to create a community of experts which votes for the projects or any other content is trustful. All symmetric reputation systems are proven to be sybil-vulnerable. So we need to make a sybil creation unaffordably expensive or use some sybil-protection mechanisms.

#### Actors 
One of the mechanisms to create sybil-proof system is to set some seed. We need an initial reputation which is set to some actors whose identity is proven. Such actors can be project owners and some kernel of community.
Every actor should be able to post some content, both articles or comments. Their reputation or karma depends on votes given by other actors to the posted content.
Also, every user, maybe after some preconditions, should be able to review projects and get an approves from the project owners.

*todo: which power the priveledge user has, how regular user turn to be priviledged*



#### Problems 
   -	Sybil attack
   -	Allowance to vote to users who are affilated with some project or content author. How to distinguish opinion from biased vote?
   -	More sophisticated sybil-attack, where sybils are reposting good comments or posts
   
#### Structure
- Ways to prove an identity.
- Karma growing curve.


#### Terms 
*describe each*

- Project (camp) - idea is the essence of innovation, the idea that seek to meet the reality is a project. 
- User - anybody who is logged in to the system
- Karma - points attached to some user identity to depict his reputation in the system. 
- Vote - 
- Kudos - 

#### Problems to solve

Second season of hack.ether.camp was using the links to social accounts as primary prove of identity. Using the platforms like microworkers.com camp owners could create bot-like voting accounts for 0.5-1 USD each. Let's take that we need sybil cloud size larger then than half of honest voting community.
If we will require more sophisticated proof of identity and the price will increase up to 10 USD, attacker will need to pay around 1500 USD to perform his attack.
We need to determine if sybil attack price is high enough.

Also, most of the votes for the top camps were given by users which were registered at less then one day before vote. Obviously, most of them were affilated with project in some way. Such way of voting helps to win heavily promoted camps which are not always the best. So, we need to put some barrier for the newcomers. They have to prove their potential value for the community before receiving voting rights.

#### Karma gain system

Statuses:
- 1. Newcomer, karma 0-99, max gain daily 20.
- 2. Voter, karma 100-5000, can vote for the projects and content, max gain daily 100.
- 3. Elder, karma >5000, can also downvote content or even punish the untrustful users. Karma >5000 cannot affect your project voting power, it's only applyable to content moderation.

*Should there be a daily upvote limit which can be done by particular user?

Every normal person starts with 0 karma. It should be quite hard to get a voter status, because newcomers cannot harm the system badly. They only can create a number of spam posts which can be easily hidden by other users. 
There should be some automated spam filter which can be optionally enabled by any user to hide
*Should there be a mechanism of automated massive downvote of spammers gang?

`Karma gain formula: (voter karma) / 25`

*Main problem here: any voter can decide to start growing an army of sybils, because he has a right to upvote. He cannot vote for himself, but he needs the reasons to give votes fairly and not to his sybils

There are some ways to prevent sybil attacks in this scheme. 

Growing of sybils can be made too slow by additional constraints. Which can be:
- To get some karma you have to get at least 2 upvotes. 
- There can be activity coefficient which will reward constantly high activity, like +2% to karma if there were upvoted content in the last days, up to 10%, or decrease up to 10% if there was no activity in last 5 days.
- After 3 days in a row of upvoting specific person by another specific person there should be cooldown for 1 or 2 days. So one user cannot be upvoted too much by single voter or even elder. 

Another way to prevent such attack is rewarding upvotes for the good content. If some content which you've upvoted then was heavily promoted by other users you also receive some additional karma.


Additional options: 
- Social accont link can be some prove of your identity, so it will give you *+5%* to karma grow per each linked account with more than 100 followers.
- Karma growth can be boosted during the first weeks or months after the system launch to help the community grow more intensively.

#### HKG deposit

* need to be double checked, because boosting karma growth by deposit could help sybils creation in some circumstances

Deposit can be another way to prevent sybil entities creation.
E.g. math can be:

`karma +=   karmaGain * 2^a`

while the 2^a depends on HKG you deposit

- 50,000HKG => a = 2
- 100,000HKG => a=3
- 200,000HKG => a=4
- 1M  => a=5

#### Hide button

If voter considers that some content is spam or offensive, he can press 'Hide' button, so he will not see this comment or post in the future. After getting *(voters karma) / 25 == 100* the author of content gets a cooldown for karma acquiring for 1 day. (Effect can be worse if he will get more 'spam' flags).

#### Content posting and upvoting rewards

Good content should be rewarded. For every 10 upvotes user receives 1 HKG to his address.

#### Gas payers

Voters are gas payers. When user becames voter he receives some Ether to cast, for example, 10 upvotes. If he generates good content afterwards, he receives HKG and then can exchange it for Ether to give more votes.

#### Kudos as karma function

Kudos amount is a linear function from karma.

`kudos = karma / 10`

#### Project reviews
Every user can write a review to any published project. Any owner of this project can approve the review and make it visible to other community members (*should the review be hidden before the approve or there should be another tab for unapproved reviews?*). An author of the review receives some fixed amount of karma in the case of approve. It can be max daily amount for a newcomer, 20 karma.
All the reviews can recieve normal votes from the community after the approval like any other post or comment.

Project reviews suppose to be a mechanism which will help to accumulate initial karma value by community new members. 

- *Should there be overall karma daily limit? Can there be some attack on it if many bots drain all the daily karma?*
- *First review for the particular project can give more karma. It will stimulate to give the reviews for all projects.*

 _**Review Recognition** - We propose to give project option to recognize the review. Review recognition doesn't say that 
 the project agrees with it but it gives some mechanism to encourage reviewers to be constructive in their critics. *todo: - review recognition should be rewarded in karma or in hackergold*

#### Notes on technical implementation
Speaking of implementation of this system it looks that best option will be pluggable contracts system. All the karma and kudos gain coefficients should be stored in separate structures. System should be changeable by submitting proposals which can be accepted or declined by the community. Approved proposal will be inserted in an array of proposals. Proporsal can be removed from an array by similiar procedure.

**External tools**

All contract data is open, so any user is able to analyze the interactions between actors. External social graph analyzing tool can be a good help for an ordinary user who wants to see if there are exist evidences of unfair interaction. E.g. user can see if there are sybil clusters and who is a owner of this cluster. None of this tools can be a automated judge which changes some user karma, but it may advise to do so. 

===

#### Points to think about 

- [ ] Projects may invite reviewers setting bounty pay for each review 
