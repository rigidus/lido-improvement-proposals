---
lip: 3
title: Imporvements DAO Governance
status: WIP (draft)
author: Mikhail Glukhov (@rigidus)
discussions-to: none
created: 2021-03-20
---

# Motivation

Some of the problems of DAO governance became apparent in the context of the high gas price and analysis of the actors' behavior. We can solve them with relatively little effort.

## Problem with current governance process

* The cost of voting on non-contentious issues (e.g. increasing the number of validator keys) is excessive
* Voting does not follow real ebb and flow of the governance and is often used only to rubberstamp an existing consensus.
* There is a theoretical vulnerability of the current voting process, when an attacker votes above the threshold number of tokens "at the last moment" to carry out his proposal
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

Due to security considerations this method should be rate limited voting and should not be used for time sensitive proposals

A few kinds of poroposal that qualify for easy-track:
- [] Key limit increase requests from validators (the validator asks for the key limit increase, if there is no objection - the keys are provided)
- [] Grant programm (grants are allowed for the LEGO gnosis safe multisig, if there is no objection)
- [] Regular payments of rewards
- Regular insurance payments (voting calls a specific function of transferring a limited amount of money)

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

## Monitoring of the voting

To observe voting and activate voting results, there must be a transparent method available to participants. At the moment, activation is performed manually. We can create a resource that will track the current votes, the strength of votes and enactment

# Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
