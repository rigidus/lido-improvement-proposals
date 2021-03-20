---
lip: 3
title: Imporvements DAO Governance with Curve Voting
status: WIP
author: Mikhail Glukhov (@rigidus)
discussions-to: none
created: 2021-03-20
---

# Problem with current governance process

* The cost of voting on technical issues (increasing the number of validator keys) is excessive compared to the benefit
* Voting does not work like real governance and is often used only to confirm decisions that have already been made.
* There is a theoretical vulnerability of the current voting process, when an attacker votes above the threshold number of tokens "at the last moment" to carry out his proposal
* There is no limit on the number of votes that can be submitted, which provokes spam
* It is necessary to monitor the enactment of votes

## How they can be solved

- Split the voting process into "general voting" and rate limited "easy-tracks" for technical confirmation votes. Easy-tracks need for:
  - Key requests from validators
  - Grant programm
  - Regular insurance payments (limited amount)
  - Regular payments of rewards
- Implement the process of transition from easy-track to general voting, if objections are found
- For "general voting" implement a minimum time between the attempts of one user to create a new vote
- Require a minimum threshold of ownership of LDO tokens to create a vote. These tokens can be locked at the time of voting
- Calculate the strength of the vote as a function decreasing with the voting time
- Create monitoring of the voting and enactment

## Proposal

...

## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
