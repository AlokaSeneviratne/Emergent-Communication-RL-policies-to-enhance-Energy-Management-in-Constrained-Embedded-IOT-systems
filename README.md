# Emergent Communication based RL Policies for Energy-Constrained IoT Systems

Using emergent communication based reinforcement learning policies to improve energy management in resource-constrained embedded IoT systems. Distributed battery-powered nodes learn when to sense, transmit, and listen rather than following fixed schedules, extending operating life under partial observability and tight energy budgets.

## Motivation

Battery-powered embedded sensor nodes spend most of their energy on sensing and radio activity. The standard ways to control that cost, duty cycling on a fixed schedule and threshold-based triggering, are hand-designed and static. They cannot adapt to the moment-to-moment state of the system, so they either waste energy staying active when nothing is happening or miss events when they happen to be asleep.

This project treats the communication and sensing schedule as something the nodes learn, not something an engineer fixes in advance. Each node decides for itself when to sense, when to publish information to its peers, and when to subscribe and listen, based only on its own partial view of the system. The decision policy is shaped purely by team-level outcomes and an explicit energy budget, so a useful, selective communication protocol emerges from learning rather than from manual protocol design.

## Problem framing

The system is modelled as a cooperative decentralised partially observable Markov decision process (Dec-POMDP). A team of nodes shares a single objective but each node observes only a local, noisy slice of the environment and acts on its own history. Energy is a non-renewable resource: every sense and every transmission draws down a fixed battery, and once it is gone the node is dead for the rest of the episode.

The learning objective balances two things in tension:

- Task performance: detect the events of interest early and reliably.
- Energy cost: keep total sensing and radio activity low enough to extend operating life.

Because exact solutions to a Dec-POMDP are intractable at any practical scale, the policies are trained with multi-agent reinforcement learning, specifically multi-agent proximal policy optimisation (MAPPO), with a recurrent actor that approximates each node's belief over the hidden state.

## Approach

1. **Emergent publish-subscribe communication.** Nodes are given the ability to broadcast and to subscribe, and are rewarded only for team outcomes. The content, timing, and selectivity of communication arise from what the nodes find useful, not from a fixed protocol.
2. **Energy-aware reward.** Sensing and transmission carry explicit per-step energy costs drawn from realistic device figures. Running out of battery is penalised, which pushes the policy toward spending energy only when it is informative.
3. **Partial observability.** Each node sees a local observation and the flags published by peers. There is no global state, so coordination has to be learned through what the nodes choose to communicate.
4. **Decentralised execution.** Training can use shared information, but at execution time each node runs its own policy on its own observations, which is what a real distributed deployment requires.

## Relationship to prior work

This repository generalises an earlier University of Oulu Master's thesis that applied the same idea to a single domain: three battery-powered wearable IMU nodes learning an emergent publish-subscribe protocol to detect Freezing of Gait early while conserving energy. That thesis later contributed a calibrated semi-Markov HMM framework for generating controllable synthetic sensor episodes, which can supply unlimited labelled training data for the agents here without further real data collection.

The intent of this repository is to lift that domain-specific prototype into a general method for energy management in constrained embedded IoT systems, where Freezing of Gait detection becomes one case study among others rather than the whole scope.

## Status

Active research. The direction and formulation are settled; the codebase and experiments are under development. Expect the structure and interfaces to change as the general framework takes shape.

## Planned components

- Environment: a configurable Dec-POMDP wrapper with a pluggable event source and a realistic energy model.
- Trainer: MAPPO with a recurrent actor and a centralised critic.
- Synthetic episode source: the semi-Markov HMM generator from the predecessor thesis, used to produce labelled training episodes on demand.
- Evaluation: event detection rate, false alarm rate, detection latency, and battery survival, reported against fixed-schedule and threshold baselines.

## References

The formulation draws on the standard Dec-POMDP and emergent communication literature, including the concise treatment of decentralised POMDPs, the NEXP-completeness result that rules out exact solvers, MAPPO as the training method, and early end-to-end emergent communication in deep multi-agent reinforcement learning. Full citations will accompany the accompanying write-up.
