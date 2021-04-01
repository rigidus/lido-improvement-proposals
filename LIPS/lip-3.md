---
lip: 3
title: Improvements DAO Governance
status: WIP (draft)
author: Mikhail Glukhov (@rigidus)
discussions-to: none
created: 2021-03-20
---

# Motivation

Some of the problems of DAO governance became apparent in the context of the high gas price and analysis of the actors' behavior. We can solve them with relatively little effort.

## Problem with the current governance process

* The cost of voting on non-contentious issues (e.g. increasing the number of validator keys) is excessive
* Voting does not follow the real ebb and flow of the governance and is often used only to rubberstamp an existing consensus.
* There is a theoretical vulnerability of the current voting process when an attacker votes above the threshold number of tokens "at the last moment" to carry out his proposal
* There is no limit on the number of votes that can be submitted, which makes voting spam a potential issue
* It is necessary to monitor the enactment of votes

## How they can be solved

- Split the voting process into "general voting" and rate-limited "easy-tracks" for non-contentious domain-specific confirmation votes.
- Implement the process of transition from easy-track to general voting, if objections are found
- For "general voting" implement a minimum time between the attempts of one user to create a new vote
- Require a minimum threshold of ownership of LDO tokens to create a vote. These tokens can be locked at the time of voting
- Create monitoring of the voting and enactment

# Proposal

To improve the voting process and eliminate shortcomings, it is proposed to make the changes described below

## Easy-track for non-contentious votes

Most of the votes are non-contentious and are held with a minimum number of votes to minimize gas costs. To improve the efficiency of such voting, it is proposed to organize a cheap easy-track process.

In this process, a proposal is passed if it is submitted by a proposer with a specific role and no objection has been received.

We can set up a minimum objection threshold that will make a fallback to the general vote, or a plain dismissal of the proposal.

Due to security considerations, this method should be rate-limited voting and should not be used for time-sensitive proposals

A few kinds of the proposal that qualify for easy-track:
- Key limit increase requests from validators (the validator asks for the key limit increase, if there is no objection - the keys are provided). Filing an objection may require staking.
- Grant programs (grants are allowed for the LEGO gnosis safe multisig if there is no objection)
- Regular payments of rewards
- Regular insurance payments (voting calls a specific function of transferring a limited amount of money)

## Easy-track voting starting

To start the easy-track voting, the contract function must be called. Calls should be limited, for example:
- An easy-track for grants can be triggered by a threshold multisig or Gnosis Safe multisig wallet. To save gas, we can initiate a vote with one transaction that contains proof of K signatures from N (threshold multisig) 
- Regular payments can be called by an Aragon agent 
- Any validator can request keys (for itself) using Node Operator Registry

When voting has started, monitoring must create a notification so that participants can be notified 

## Voting operation process

Active voting exists until its lifetime has expired. 

To send an objection, the owner of the LDO tokens must make a transaction specifying these tokens. 

There is an attack in which an objector can sell voted tokens and immediately buy new ones to vote again. To prevent this, the easy-track contract must make a call to the token manager to prohibit the transfer of these tokens until the end of the voting. An even more economical way is to use balances at the time of the block in which voting began. That is, only tokens that have not been moved since the start of voting can vote.

## End of voting 

When the voting period has expired, the easy-track counts the objections. If they exceed the threshold, then the vote is considered unsuccessful. 

For MVP purposes, we do not need to do an automatic fallback to the general vote, but technically it only requires a call to Aragon. 

## Minimum time between the attempts of one user to create a new vote and minimum threshold requirement to start voting

Currently, the one who starts the voting does not incur expenses and does not place a bet. This opens up opportunities for spam. We may require small expenses or blocking tokens to open the voting process. This will make it difficult to create many polls.

## Monitoring of the voting

To observe voting and activate voting results, there must be a transparent method available to participants. At the moment, activation is performed manually. We can create a resource that will track the current votes, the strength of votes, and the enactment

## Details about Gnosis Safe and Snapshot

Gnosis Safe is a multi-signature wallet suitable for grant distribution. To transfer money, multiple signatures of several responsible persons are required. These responsible persons ensure the security of grant budgets. The DAO elects these responsible persons (delegates) through a general vote. Delegates can be removed by a general vote. When such a vote is initiated, the distribution authority shall be suspended. 

Snapshot allows communities to do token holder votes off-chain. The voting result is verifiable, and the voting process is tamper-resistant (with votes and relayer receipts stored in IPFS). The main weakness of this approach is that the votes are not submitted and broadcast on-chain, so a trusted entity is required to review the final vote count and enact the results. The simplest solution is to trust the delegates, but we can use other methods that complicate the collision, for example, randomly choosing a trusted entity or using a third party service as an arbiter (SafeSnap). 

To prevent malicious actions, users will need to make a security deposit when triggering proposals.

## Possible attacks and defense

A malicious owner of LDO tokens can block all voting easy-track if his tokens exceed the threshold. A DAO can defend against them by putting to a general vote the issue of burning its LDO tokens for malicious conduct. 

# Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
