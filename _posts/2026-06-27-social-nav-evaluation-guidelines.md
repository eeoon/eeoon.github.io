---
layout: post
title: "Principles and Guidelines for Evaluating Social Robot Navigation Algorithms (ACM THRI 2025)"
date: 2026-06-27 09:00:00 +0900
description: "A consensus-style evaluation standard paper in which 31 collaborators propose a definition of social robot navigation, eight principles, guidelines for metrics, scenarios, benchmarks, datasets, and simulators, and a common metrics API"
tags: [social-navigation, navigation]
categories: paper-review
thumbnail:
giscus_comments: false
related_posts: false
toc:
  sidebar: left
---

| Item | Details |
|---|---|
| Authors | Anthony Francis, Claudia Pérez-D'Arpino, Chengshu Li, Fei Xia, Alexandre Alahi, Rachid Alami, Aniket Bera, Abhijat Biswas, Joydeep Biswas, Rohan Chandra, Hao-Tien Lewis Chiang, Michael Everett, Sehoon Ha, Justin Hart, Jonathan P. How, Haresh Karnan, Tsang-Wei Edward Lee, Luis J. Manso, Reuth Mirsky, Sören Pirk, Phani Teja Singamaneni, Peter Stone, Ada V. Taylor, Peter Trautman, Nathan Tsoi, Marynel Vázquez, Xuesu Xiao, Peng Xu, Naoki Yokoyama, Alexander Toshev, Roberto Martín-Martín (about 20 institutions including Google, Stanford, and UT Austin) |
| Venue | ACM Transactions on Human-Robot Interaction (THRI) Vol.14 No.2, Art.34, 2025-02 (arXiv 2306.16740, submitted 2023-06, v4 2023-09) |
| Links | [arXiv](https://arxiv.org/abs/2306.16740) · [Code](https://github.com/SocialNav/SocialNavAPI) |

## One-line Summary

This paper does not propose a new algorithm. To evaluate social robot navigation (driving robots in spaces shared with people) fairly and reproducibly, a large 31-author collaboration presents a consensus-style survey/position paper with (a) a definition of a "socially navigating robot" and eight principles (safety, comfort, legibility, politeness, social norms, understanding other agents, proactivity, contextual appropriateness), (b) a bundle of guidelines for metrics, scenarios, benchmarks, datasets, and simulators, and (c) a common metrics API for comparing results across simulators. In short, it is a paper about "how to establish the evaluation standard of the field."

## Lineage and Trends

Social navigation has a long history, but there was no consensus on "what counts as social," and evaluation methods varied from lab to lab. In contrast to computer vision (ImageNet) and NLP (GLUE and the like), which advanced explosively on shared benchmarks, social navigation had close to 100 different metrics, incompatible simulators, and non-comparable datasets, making it hard to say "which method is better."

The key prior lines are as follows.

- Kruse et al. (2013), "Human-Aware Robot Navigation: A Survey": an early survey that defined comfort, naturalness, and sociability. This is the starting point that the paper expands into eight principles.
- Rios-Martinez et al. (2015): a survey linking proxemics to socially-aware navigation. The source of the personal-space notions (intimate 0.45 m / personal 1.2 m).
- Gao & Huang (2021), "Evaluation of Socially-Aware Robot Navigation": reviewed 177 papers and tallied metrics, scenarios, and datasets. The evaluation-centric survey this paper builds on directly.
- Mavrogiannis et al. (2021), "Core Challenges of Social Robot Navigation": organizes the core challenges in navigation algorithms, human behavior models, and evaluation.
- Mirsky et al. (2021): the notion of conflict (a short-term encounter that would lead to collision without intervention). The basis for P6 and P7.

The direct trigger was the 2022 Social Navigation Symposium. This paper is the outcome of consolidating discussions in which diverse definitions and methods clashed into a consensus taxonomy. It is not an individual algorithm paper but a position paper proposing an "evaluation constitution" for the field, and what is new is that it (1) operationalizes the vagueness of "social" into eight principles, (2) standardizes evaluation tools into a single common taxonomy with scenario cards and three-letter metric codes, and (3) takes the first step toward a unified metrics API with actual code (SocialNavAPI).

Since then, "Characterizing the Complexity of Social Robot Navigation Scenarios" (2024), "How Do Robot Experts Measure the Success of Social Robot Navigation?" (HRI 2024), and many VLM-based social navigation studies (AutoSpatial, Narrate2Nav, etc.) have adopted these guidelines as their evaluation frame. The tools reviewed in the paper are also actively evolving: HuNavSim (ROS2 SFM + Behavior Tree, RA-L 2023, follow-up 2.0 in 2025), Arena-Rosnav 2.0, SocialGym 2.0, SEAN 2.0, SocNavBench, and the iGibson Challenge.

## Problem and Motivation

Deploying robots widely in human environments requires the ability to navigate among people, but evaluating that ability is hard. Unlike traditional navigation in static environments, it entangles (1) dynamic human agents and (2) humans' subjective perception of whether the robot's behavior is appropriate. Social navigation lacked the clear, reproducible, accessible benchmarks that accelerated CV/NLP, so algorithms could not be compared fairly, their limits exposed, or promising directions identified. The intended readers are researchers developing and comparing social navigation policies, builders of benchmarks, simulators, and datasets, and industry preparing real-robot deployments.

## Key Ideas and Method

The core definition is: "a socially navigating robot is one that achieves its own navigation goal while modifying its behavior so that the experience of surrounding agents is not degraded, or is even improved." This is operationalized through eight principles, evaluation guidelines, and a common API.

### The Eight Principles (P1-P8)

From an optimization viewpoint, P1-P7 are additional objectives to optimize on top of the primary goal (reaching the destination), while P8 (context) is a meta-factor that decides which principles to weight more heavily at each moment. The principles are not fully orthogonal (higher legibility also raises safety and comfort, for example).

| Principle | Content |
|---|---|
| P1 Safety | Do no harm to humans, other robots, or the environment. Collision avoidance is central, but damage to walls and fixtures and accidents induced in other robots are included |
| P2 Comfort | Do not cause annoyance or stress. Respect proxemics (intimate 0.45 m, personal 1.2 m); avoid sudden stops, jitter, and excessive speed |
| P3 Legibility | Make the goal inferable from behavior alone (Dragan's legibility, distinct from predictability). Robots that can communicate may state intent explicitly |
| P4 Politeness | Physical politeness (not cutting in) + communicative politeness ("on your left") |
| P5 Social Competency | Follow conventions such as keeping to the right. Not an optimization problem but adherence to consensus |
| P6 Agent Understanding | Predict and accommodate other agents' intentions and actions, e.g. judging whether to pass between two people in conversation |
| P7 Proactivity | Yield or propose first to resolve deadlocks (four-way deadlock, narrow-door contention). Being too conservative leads to the freezing robot problem |
| P8 Contextual Appropriateness | Weighting of the above principles changes with culture, diversity, environment (geometric/operational), task, and interpersonal context. E.g. a hospital crash cart prioritizes task success over politeness |

### Research Methodology and Lifecycle

Research types are distinguished as in-the-wild studies → robot deployments → laboratory experiments → social navigation scenarios → staged social interactions, and these are tied into a single lifecycle (data collection → issue discovery → lab experiments → scenario development → benchmarking → challenges). "Wild data" and "controlled scenarios" are treated as a cycle rather than opposites. The real-world study guidelines are R1 (preserve safety), R2 (respect participants/IRB), and R3 (clear scientific goals).

### Common Taxonomy

It organizes general factors shared by scenarios, benchmarks, datasets, and simulators (context, physical environment, human user type, human behavior, robot task/role, scenario, coverage, hardware platform, sensors/actuators, communication, collected data, metrics, behavior authoring, sim vs real) and tool-specific factors (datasets: number of trajectories, distance traveled, number of encounters, viewpoint, annotations, privacy / benchmarks: platform, dataset, baselines, leaderboard, downloadability / simulators: abstraction level, scene/agent representation, physics/robot/pedestrian fidelity).

### Metric Classification (Three-letter Codes)

Every metric is classified along three axes and given a three-letter code: (1) variable type: Social / Non-social / All-encompassing, (2) nature: Hand-crafted / Sensor / Questionnaire / Learned, etc., (3) time scope: Step-wise / Task-wise. For example, Success Rate = NHT, an instantaneous facial reaction = SSS, and an overall quality questionnaire = AQT.

The recommended hand-crafted metrics (Table I) fall into two families.

- Success family (so as not to regress on traditional navigation performance): Success/SR, Collision/CR (distinguishing walls, agents, humans), Timeout, Failure to Progress, Stalled Time, Time to goal, Path Length, SPL.
- Quality/social family: velocity/acceleration/jerk (min/avg/max), Clearing Distance, Space Compliance (Personal Space Compliance when the threshold is 0.5 m), Minimum Distance to Human, Minimum Time-to-Collision, Aggregated Time.

The recommended flow is this. Surveyed (questionnaire) metrics are the gold standard but expensive and non-reproducible, so first validate in simulation with a common subset of hand-crafted metrics (M2), then evaluate sociality with validated questionnaires (M5), and always report distribution information (histograms) alongside (M8). The metric guidelines are M1-M8 (report standard metrics, validate first with algorithmic metrics, parameterize to context, acceptance-test with learned metrics, use validated questionnaires, remove bias, repeat analyses, report in depth).

### Scenario Cards and Benchmark/Simulator Guidelines

Modeled on ML "model cards," it proposes the Social Navigation Scenario Card (metadata / definition / usage guide). The common scenario list (Table III) includes FRONTAL APPROACH, OVERTAKING, INTERSECTION, BLIND CORNER, NARROW DOORWAY, ENTERING/EXITING ELEVATOR, JOINING/LEAVING GROUP, FOLLOWING/LEADING, CROWD/PARALLEL/PERPENDICULAR TRAFFIC, OBJECT HANDOVER, CRASH CART, and others. It presents scenario guidelines N1-N8, benchmark guidelines B1-B6 (evaluate social behavior, quantitative metrics, baselines, efficiency/reproducibility/extensibility, grounding in human data, validated tools), dataset guidelines D1-D8, and simulator guidelines S1-S9 (standard API, standard metrics, extensibility, dataset/benchmark/labeling support, common morphologies, detailed pedestrians, behavior authoring).

### Unified Metrics API

A common high-level metrics API that gathers the differing outputs of each simulator into one. The input specification (pedestrian/robot/obstacle data) is defined as ROS messages + OpenAI Gym info + JSON Schema, and standard C++/Python libraries compute the Table I metrics and emit a single output format. Each simulator, robot, or dataset only needs to write bridge code.

By analogy, just as CV had ImageNet and NLP had GLUE, this is an attempt to "build a common scorecard for social navigation." However, since what is being scored is people's subjective comfort, it cannot be reduced to a single number, so the paper simultaneously presents eight scoring items (principles), scoring rules per item (metric/scenario/benchmark guidelines), and an adapter (API) that converts each simulator's differently formatted answer sheet into the same form.

## Experiments and Results

This paper does not train or evaluate a new algorithm. Instead, its output is a set of systematic meta-analysis tables of existing tools.

- **Comparison of 9 benchmarks (Table IV):** ArenaBench, CrowdBot, DynaBarn, gym-collision-avoidance, HuNavSim, iGibson, SocNavBench, SEANavBench, and Social Navigation Protocol are evaluated in a grid by classification, context, physical environment, user type, scenarios, corner-case coverage, simulation platform, dataset, human behavior authoring, fidelity, supported robots, communication, leaderboard, last update, and compliance with B1-B6.
- **16 datasets (Table V):** JRDB, THOR, SCAND, MuSoHu, LCAS, ETH/UCY, TrajNet++, EIFPD, SDD, EFL, WILDTRACK, CrowdBot, DynaBarn, SocNav1/2, SACSoN, and SG-LSTM are organized by context, environment, amount collected, scenarios, platform, sensors, task, and metrics.
- **13 simulators (Table VI):** CrowdBot, CrowdNav, DynaBarn, gym-collision-avoidance, HuNavSim, iGibson, InHuS, IMHuS, Menge-ROS, PedSim-ROS, SEAN 2.0, SocialGym 2.0, and SocNavBench are compared by focus, platform, agent/scene representation, physics/robot/pedestrian fidelity, pedestrian reaction (SFM, ORCA, replay), and interoperability (Gym, ROS).
- As anecdotes of scale, 1,000 km of indoor campus driving and more than 3,000 km of Google deployments are cited, and a sample of 30 or more trials is recommended as a rule of thumb for real-robot runs.

The three most important outputs are (1) the eight principles + unified taxonomy, (2) the actionable guideline code bundle (P/R/M/N/B/D/S) + Scenario Card + three-letter metric classification and recommended metric table, and (3) the meta-comparison tables of 9 benchmarks / 16 datasets / 13 simulators + the unified metrics API. The result is not a quantitative SOTA contest but a consensus on "what to measure and how."

**Points to keep in mind when reading.** The eight-principle definitions are well organized on the basis of symposium consensus, but the problem of operationalizing "experience not degraded or improved" into something measurable remains vague (as the authors acknowledge). Most guidelines are at the "should/recommend" level with almost no enforcement or quantitative thresholds (e.g. the Space Compliance threshold is only said to be reported per context). The weakest claim is the unified API. At the time of checking, the `SocialNav/SocialNavAPI` repo had one commit and one file (main_api.py), no README or license, and the C++/Python libraries, JSON Schema, and SEAN/SocialGym/DynaBarn bridges were described as "under development," so no evidence of a usable implementation could be confirmed. The design blueprint is clear, but adoption and validation are unproven. On the other hand, the paper itself states that "we could not find a way to conclude quantitatively that one metric is better than another," "learned metrics are not yet ready for adoption," and "simulated pedestrian reactions (replay vs SFM/ORCA) are both incomplete," so there is little overstatement and the acknowledgment of limitations is a strength.

## Conclusion and Significance

Social robot navigation adds two hard problems to every problem of traditional navigation: "how the robot should coexist with moving humans" and "how humans respond to that participation." This paper provides a shared language and consensus frame for its evaluation (eight principles + taxonomy + guidelines + Scenario Card + common API), laying a foundation that makes fragmented evaluations comparable. The core message is that "infrastructure for measuring social navigation policies fairly and reproducibly matters as much as building good policies."

From a robotics practice viewpoint, the takeaways are as follows.

- **What can be used right away.** When evaluating encounters with people in autonomous mobile robot navigation in multi-floor indoor buildings, the hand-crafted metrics of Table I (SR, collisions, SPL, Space Compliance, minimum distance, TTC, etc.) and the Scenario Card format can be adopted at little cost. In particular, scenario definitions such as NARROW DOORWAY, ENTERING/EXITING ELEVATOR, and BLIND CORNER can be carried over directly as regression test items for indoor robots.
- **Simulation benchmarking.** The tools reviewed in the paper, HuNavSim (ROS2 + Gazebo), Arena-Rosnav 2.0, SocialGym 2.0, SEAN 2.0, SocNavBench, and iGibson, are actually downloadable and active. Setting up scenarios in them to benchmark policies is a medium-difficulty adoption path. The datasets (ETH/UCY, SCAND, JRDB, THOR, SDD, MuSoHu, SACSoN, SocNav1/2, etc.) are also readily accessible.
- **Where it gets stuck.** Using the unified SocialNavAPI directly is impractical because the implementation is immature, so bridge code effectively has to be written from scratch. Validated human questionnaires (M5/M6) require IRB, test-retest, construct validity, and repeated Cronbach's alpha validation, which is labor-intensive and costly. All-encompassing learned metrics are in a "not ready for adoption" state.
- **Structural limitations.** Simulated pedestrians are incomplete whether replayed (no reaction) or SFM/ORCA (simple models), so there is weak assurance that a policy that works in simulation transfers to real-world human reactions (sim-to-real gap). The problem of metrics drifting over time as people get used to robots (novelty effect, robot wrangler bias, STEM participant bias) and the cultural/diversity context remain open. There is also a residual unfairness in that robots that announce intent by voice or display can appear less social under standard metrics.

## References

- Paper: [arXiv 2306.16740](https://arxiv.org/abs/2306.16740) (ACM THRI Vol.14 No.2, 2025)
- Code: [SocialNav/SocialNavAPI](https://github.com/SocialNav/SocialNavAPI) (reference implementation, early stage)
- Human-Aware Robot Navigation: A Survey (Kruse et al., 2013)
- Evaluation of Socially-Aware Robot Navigation (Gao & Huang, 2021)
- Core Challenges of Social Robot Navigation: A Survey (Mavrogiannis et al., 2021)
- Legibility and Predictability of Robot Motion (Dragan et al., 2013)
- HuNavSim: A ROS 2 Human Navigation Simulator (Pérez-Higueras et al., RA-L 2023)
