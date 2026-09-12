---
layout: post
title: "Continual Learning for Traversability Prediction with Uncertainty-Aware Adaptation (RA-L 2025)"
date: 2026-06-11 09:00:00 +0900
description: Continual traversability learning that recalls past experience with a CVAE instead of storing data, filtering recalled samples by uncertainty to reduce catastrophic forgetting
tags: [traversability, continual-learning, navigation, terrain]
categories: paper-review
thumbnail:
giscus_comments: false
related_posts: false
toc:
  sidebar: left
---

| Item | Details |
|---|---|
| Authors | Hojin Lee, Yunho Lee, Daniel A. Duecker, Cheolhyeon Kwon (UNIST HMCL, TUM MIRMI) |
| Venue | IEEE Robotics and Automation Letters (RA-L) vol. 10 no. 11, 2025 |
| Links | [DOI](https://doi.org/10.1109/LRA.2025.3619687) · [Code](https://github.com/HMCL-UNIST/Continual-Traversability-Learning) |

## One-line Summary

This is a continual learning framework that addresses catastrophic forgetting, where an off-road robot that learns terrains such as asphalt, gravel, sand, and forest sequentially forgets old terrains while learning new ones. Instead of storing past data, it generates synthetic past samples with a conditional VAE and replays them, but uses the predictive uncertainty of those samples as a measure to select only trustworthy ones for updating the predictor and the generator. The memory cost is fixed at the size of the model parameters regardless of the number of environments, and on a real robot, after learning five environments and returning to the first, it achieved the same navigation performance as the upper-bound model that retained all data.

## Lineage and Trends

The paper sits at the intersection of "learning-based traversability prediction" (WayFAST, EVORA) and "continual learning" (Learning without Forgetting, continual learning surveys). Its distinguishing feature is bringing generative replay into off-road traversability.

The prior works that serve as direct comparisons are as follows.

- **WVN** (Frey 2023): Pursues only fast online adaptation and ignores preservation of past experience, so forgetting occurs.
- **IMOST** (Ma 2024): Clusters and stores visual features in an incremental dynamic memory buffer. Retention is good, but memory keeps growing in proportion to environmental diversity.
- **Self-supervised online continual learning** (Yoon 2024): Uses a fixed-size buffer, so there is a risk of discarding useful information when inserting new data.

In other words, replay methods carry a "scalability versus expressiveness" trade-off. This paper synthesizes the past with a CVAE generator instead of storing data, and compensates for the chronic weakness of generative continual learning, namely "not accounting for the reliability of generated samples," with uncertainty filtering. It connects directly to the authors' prior work (3D off-road uncertainty-aware navigation, terrain-aware kinodynamic MPPI), and the conclusion mentions physics-informed foundation models (the PIETRA family) as a future direction.

## Problem and Motivation

1. **Generalization failure:** Learning-based traversability predictors work well under conditions similar to the training environment, but performance drops sharply on new terrain.
2. **Side effect of adaptation = forgetting:** Updating the model for new terrain overwrites past experience with new experience, degrading performance in previous environments.
3. **Limits of existing solutions:** Experience replay must keep past data in memory, so memory explodes (IMOST) or information is lost (fixed buffer). This does not suit resource-constrained mobile robots.
4. **Gap in generative continual learning:** Synthesizing the past with a generative model removes the need for storage, but if synthetic samples deviate from the true distribution they actually corrupt the model. Existing generative continual learning ignored this uncertainty.

## Key Ideas and Method

### Problem setting: domain-incremental continual learning

This is a domain-incremental setting where the learning task (traversability prediction) is fixed and only the input distribution (terrain characteristics) changes with each deployment. The traction parameter $$\xi = [\xi^x, \xi^z]$$ is defined as the measure of traversability. It is the linear and angular discrepancy between commanded and actual velocity, and is multiplied into unicycle kinematics to model skid-steering or legged robots. The prediction model $$T$$ takes the terrain feature $$\phi$$ and velocity $$v$$ and outputs a Gaussian distribution of traction $$\mathcal{N}(\mu, \sigma^2)$$.

### Self-supervised labeling: EKF

The robot drives itself and measures whether it actually moved as much as commanded. Traction is modeled as a random walk $$\xi_{k+1} = \xi_k + \nu$$ and estimated from odometry with an extended Kalman filter to automatically generate training labels. This was inspired by WayFAST's moving-horizon estimator.

### Terrain feature processing: DINOv2 + autoencoder + grid map

Per-pixel 384-dimensional features are extracted from RGB with a DINOv2 backbone. Projecting this dimensionality directly onto a grid map would explode compute and memory, so an autoencoder trained in an unsupervised manner with a reconstruction loss on large-scale off-road data compresses it to a low dimension ($$p=3$$ in the experiments). LiDAR point clouds are processed into an elevation grid map, and vision and geometry are combined into a multi-modal grid map based on Multi-Modal Elevation Mapping (0.25 m resolution, 20 Hz). The terrain features $$\phi$$ beneath the four wheel contact points are extracted to build the dataset $$D = \{(\phi_j, v_j, \xi_j)\}$$.

### Uncertainty-aware prediction: probabilistic ensemble

$$M=5$$ MLPs of identical structure ([16,32,32,16] + LeakyReLU) are trained independently. It is like asking M experts the same question and looking at the mean and their disagreement. Uncertainty is split into two kinds.

$$
\sigma^2 = \sigma^2_{ale} + \sigma^2_{epi}
$$

Aleatoric uncertainty is the mean of the $$\sigma^2$$ output by each member (inherent data noise), and epistemic uncertainty is the variance of the mean predictions across members (lack of knowledge).

### Generative experience recall model R: CVAE

The idea is that even if you throw away the photo album (data), you can still recall the past if you keep a painter (generator) who can depict that time. Since velocity $$v$$ is a physical quantity within hardware limits, $$v$$ is sampled first, and conditioned on it the terrain feature $$\phi_{recall}$$ is generated by the decoder $$p_{dec}(\phi \mid v, z)$$. The recalled input $$(\phi_{recall}, v_{recall})$$ is passed through the predictor $$T$$ to obtain both the output $$\xi_{recall}$$ and its uncertainty $$\sigma_{recall}$$. A large $$\sigma_{recall}$$ is a signal that "this recalled sample lies in a region that was not well represented by the training data so far."

### Uncertainty-aware adaptation: simultaneous update of both models

At deployment $$i$$, both models are updated with the new data $$D_i$$ and the recalled data $$D_{recall}$$.

**Predictor $$T$$ update:**

$$
\mathcal{L}_{T_i} = \mathcal{L}_{NLL}(D_i) + \lambda \, \mathcal{L}_{adapt}(D_{recall})
$$

The first term is adaptation to the new environment (negative log-likelihood), and the second is preservation of the past. $$\mathcal{L}_{adapt}$$ aligns the predicted distribution with the recalled distribution using the Jensen-Shannon divergence (symmetric KL), mitigating forgetting and preventing overfitting to untrustworthy samples with large $$\sigma_{recall}$$.

**Recall model $$R$$ update:** Recalled samples are filtered with a variance threshold $$\tau$$ so that only trusted samples remain.

$$
\bar{D}_{recall} = \{(\phi_{recall}, v_{recall}) \mid \sigma_{recall} < \tau\}, \quad D_{aug} = D_i \cup \bar{D}_{recall}
$$

$$R$$ is retrained on $$D_{aug}$$. Only the trustworthy past contributes to preservation, while new data contributes to adaptation.

### Navigation: sampling-based MPC

The updated $$T$$ is plugged into an MPC. The cost is as follows.

$$
C = w_1 \cdot \text{Goal} + w_2 \cdot \sigma^2_t + w_3 \cdot |1 - \mu_t|
$$

Goal is progress toward the target, $$\sigma^2_t$$ is avoidance of uncertain regions (the core of OoD avoidance), and $$|1-\mu_t|$$ drives traction toward 1 (ideal no-slip). The stochastic dynamics are approximated by mean propagation, and it runs with horizon $$T=30$$ (3 seconds), 1024 rollouts, at 10 Hz.

## Experiments and Results

**Platform:** Clearpath Jackal (skid-steering), NVIDIA Jetson AGX Orin onboard compute, Ouster LiDAR, ZED X stereo camera, LiDAR-inertial odometry. Entirely ROS 2.

**Dataset:** Five environments: $$D_1$$ park asphalt, $$D_2$$ riverside gravel, $$D_3$$ sand, $$D_4$$ bike path, $$D_5$$ dense forest. At each site, about 10 minutes of manual driving covered both traversable and non-traversable areas. PCA visualized the distinct domain shift among the five environments.

**Metrics:** Adaptation is the test NLL on $$D_j$$ immediately after training on $$D_j$$ ($$n_{j,j}$$, lower is better). Memory retention is the Forgetting Measure (FM) $$f_{k,j} = \max(0, n_{k,j} - \min_{i<j} n_{k,i})$$, where lower means better retention.

**Six baselines:** CM (retraining on all data, upper bound), IMOST (incremental memory), WVN (fast adaptation only), LwF (distillation), NGR (an ablation of the proposed method with only the $$\tau$$ uncertainty filter removed), and Proposed. For fairness, all baselines were interfaced with the same inputs and probabilistic outputs, and hyperparameters were chosen on the Pareto front. Statistics are averages over 10 training sessions.

The three key results are as follows.

1. **Minimal forgetting without storing past data.** Among methods that do not directly access past data, the proposed method has the lowest FM. For example, retention FM for $$D_1$$ after training through $$D_5$$ is 1.45±0.13, lower than WVN 2.56, LwF 2.68, and NGR 1.82, and on par with or better than IMOST (1.55), whose memory keeps growing.
2. **Effect of uncertainty filtering demonstrated.** With the filter removed, NGR's recalled samples scatter widely from the true distribution (PCA), whereas the proposed method stays close to the real data distribution. Quantitatively, FM improves over NGR across all stages.
3. **OoD-avoiding navigation succeeded.** Driving back in environment 1 with $$T_5$$, trained sequentially from $$D_1$$ to $$D_5$$, achieved goal reaching 3/3 and OoD (grass) avoidance 3/3, equal to the upper bound CM (3/3, 3/3). IMOST, WVN, and LwF achieved 0/3 on OoD avoidance and NGR only 1/3. Because of forgetting, they misestimated OoD uncertainty and entered hazardous regions.

One point worth noting is standalone adaptation performance. The proposed method is not always first; for instance, at $$D_1 \to D_2$$ its NLL of 0.71 is worse than WVN's 0.13. Fast-adaptation methods may have the advantage in fitting a new environment immediately, but the advantage in retention (FM) puts the proposed method ahead overall. The paper acknowledges this trade-off.

## Conclusion and Significance

The contribution to the field is integrating and validating on a real robot the combination of "generative replay without data storage + uncertainty gating of generated samples" for off-road continual traversability learning. The practical core is that the memory cost is fixed to the parameters of the predictor and recall model alone, enabling long-term deployment regardless of the number of environments, and the paper demonstrated a closed loop in which traversability is physically defined as traction and coupled with MPC so that predictive uncertainty leads directly to risk-avoiding navigation.

From a robotics practice perspective, the takeaways are as follows.

- **Where it can be used:** When a robot driving on unstructured outdoor terrain is operated for a long time while moving between deployment areas. Since 10 Hz inference with an ensemble of 5 and 1024 rollouts runs on embedded-class hardware (Jetson AGX Orin), it is a realistic path to onboard continual learning.
- **Reproduction difficulty is high:** The dataset and checkpoints are not released, so real data must be collected directly; it depends on the full hardware stack of Jackal, Ouster, ZED X, and elevation mapping; and the DINOv2 autoencoder assumes separate pretraining on large-scale off-road data. The repository has no LICENSE file, so the license was not confirmed and the authors should be consulted before reusing the code. Reproducing only the core algorithm (CVAE recall + $$\tau$$ filter + JS alignment) on synthetic data is of moderate difficulty.
- **Recall quality depends on predictor uncertainty:** The $$\tau$$ filter relies on the assumption that the preceding predictor's $$\sigma$$ estimate is accurate. If the epistemic estimate is off, there is a risk of preserving wrong samples.
- **Limits of expressiveness and scale:** Whether a single CVAE can faithfully synthesize all past distributions when the number of environments and terrain diversity grow very large was only demonstrated up to five environments. Navigation trials were 3 per method and the OoD scenario was a single grass-avoidance case, which is insufficient for statistical generalization. Per-environment sensitivity and automatic tuning of $$\tau, \lambda$$ and so on also remain unresolved.

## References

- Paper: [DOI 10.1109/LRA.2025.3619687](https://doi.org/10.1109/LRA.2025.3619687) · Code: [github.com/HMCL-UNIST/Continual-Traversability-Learning](https://github.com/HMCL-UNIST/Continual-Traversability-Learning)
- Gasparino et al., WayFAST: Navigation with Predictive Traversability in the Field (2022)
- Frey et al., Fast Traversability Estimation for Wild Visual Navigation (2023)
- Ma et al., IMOST: Incremental Memory Mechanism with Online Self-Supervision for Continual Traversability Learning (2024)
- Wang et al., A Comprehensive Survey of Continual Learning (2024)
- Sohn, Lee, Yan, Learning Structured Output Representation using Deep Conditional Generative Models (2015)
