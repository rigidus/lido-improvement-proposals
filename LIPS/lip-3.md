---
lip: 3
title: Imporvements DAO Governance
status: WIP (draft)
author: Mikhail Glukhov (@rigidus)
discussions-to: none
created: 2021-03-20
---

# Motivation

Some of the problems of DAO governance became apparent in the context of the high gas price and analysis of the actor's behavior. We can solve them with relatively little effort.

## Problem with current governance process

* The cost of voting on technical issues (increasing the number of validator keys) is excessive compared to the benefit
* Voting does not work like real governance and is often used only to confirm decisions that have already been made.
* There is a theoretical vulnerability of the current voting process, when an attacker votes above the threshold number of tokens "at the last moment" to carry out his proposal
* There is no limit on the number of votes that can be submitted, which provokes spam
* It is necessary to monitor the enactment of votes

## How they can be solved

- Split the voting process into "general voting" and rate limited "easy-tracks" for technical confirmation votes.
- Implement the process of transition from easy-track to general voting, if objections are found
- For "general voting" implement a minimum time between the attempts of one user to create a new vote
- Require a minimum threshold of ownership of LDO tokens to create a vote. These tokens can be locked at the time of voting
- Calculate the strength of the vote as a function decreasing with the voting time
- Create monitoring of the voting and enactment

# Proposal

To improve the voting process and eliminate shortcomings, it is proposed to make the changes described below

## Easy-track for technical confirmation votes

Most of the votes are technical in nature and are held with a minimum number of votes to minimize gas costs. To improve the efficiency of such voting, it is proposed to organize a cheap easy-track process.

In this process, a proposal is passed if it is submitted by a proposer with a specific role and no objection has been received.

We can set up a minimum objection threshold that will make a fallback to the general vote.

This method is only suitable for rate limited voting and not suitable for time sensitive voting

It is supposed to use an easy-track voting for:
- Key requests from validators (the validator asks for the keys, if there is no objection - the keys are provided)
- Grant programm (grants are allowed for gnosis safe multisig, if there is no objection )
- Regular insurance payments (voting calls a specific function of transferring a limited amount of money)
- Regular payments of rewards

Voting process can be based on Gnosis Safe + Snapshot + SafeSnap, where "Safe owners" can block easy-track and initiate genereal voting

A more detailed diagram here: https://miro.medium.com/max/1050/0*hHO_mi5PDsTl1gcS

SafeSnap module is an oracle-based solution that looks something like this:

- A Gnosis Safe module, where anyone can create a new proposal: an array of multisend transaction payloads.
- Each proposal is a Reality.eth question asking if (1) the linked Snapshot proposal passed, (2) did the proposal include the payload, and (3) does the payload do what the proposal describes.
- If the proposal passes on Snapshot, then Reality.eth should resolve to the same outcome, and after a 24 hour cooldown period, the proposal’s transactions are executable by anyone.

## Work with objections

For each of the simplified voting processes, an objection mechanism can be proposed that would require a general vote. This mechanism will be activated if one (or many) of the objectors asks for a general vote.

## Minimum time between the attempts of one user to create a new vote and minimum threshold requirement to start voting

Currently, the one who starts the voting does not incur expenses and does not place a bet. This opens up opportunities for spam. We may require small expenses or blocking tokens to open the voting process. This will make it difficult to create many polls.

## Change strength of the vote

Currently we are using the standard Aragon voting method, where 1 token = 1 vote. We can replace this with vote weight as described below.

The corresponding scheme can be adapted from curve-aragon-voting (https://github.com/curvefi/curve-aragon-voting)

Instead of voting with token amount **a**, voting tokens are lockable in a **VotingEscrow** for a selectable locktime **t-lock**, where **T_lock < T_max**.

The voting weight is equal to:

**w** = **a** (**T** / **T_lock**)

In other words, the vote is both amount and time-weighted, where the time counted is how long the tokens cannot be moved in future

The account which locks the tokens cannot be a smart contract (because can be tradable and/or tokenized), unless it is one of whitelisted smart contracts (for example, widely used multi-signature wallets).

balanceOf() / balanceOfAt() and totalSupply() / totalSupplyAt() return the time-weighted voting weight **w** and the sum of all of those weights **W=∑w_i** respectively. Aragon can interface **VotingEscrow** as if it was a typical governance token

User voting power **w_i** is linearly decreasing since the moment of lock. So does the total voting power **W**.

### Advantages

The solution is compatible with the current voting process in Aragon and requires minimal changes to adapt

### Disadvantages

Curve-aragon-voting, which we are adapting, is not a mature solution, it is supported by 3 contributors and I am one of them, because I corrected several links to the documentation. The fact that a random person stumbled upon this does not speak in favor of the polished solution. The main work was carried out until August last year, now there are rare commits. The project does not support by big community, there are no pull requests

## Monitoring of the voting

To observe voting and activate voting results, there must be a transparent method available to participants. At the moment, activation is performed manually. We can create a resource that will track the current votes, the strength of votes and enactment

# Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
