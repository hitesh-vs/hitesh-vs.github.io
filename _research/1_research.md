---
layout: page
title: Graph Based Morphology Encoding for Robust Generalisation of RL Policies
description: FiLM-Aided Graph Transformers (FAGT) — a single humanoid locomotion policy that conditions on the robot's own URDF, so it keeps walking when link masses, actuator limits, damping or limb proportions change. MS Thesis, WPI 2026.
img: assets/img/rl_research.png
importance: 2
category: Deep Learning and Computer Vision
#related_publications: true
---

**FiLM-Aided Graph Transformers: Learning Humanoid Locomotion with Morphology-Aware Transformers**
<br>
M.S. Thesis, Robotics Engineering, Worcester Polytechnic Institute (April 2026). Advisor: Prof. Mahdi Agheli.

📄 **Full thesis:** [Read the PDF](https://drive.google.com/file/d/1DMo-htU065Fb84vncxe2e-Iy5C9iW3cI/view?usp=sharing)

---

## TL;DR

A locomotion policy trained with deep RL is tied to the exact robot it was trained on. Change a link's mass, weaken a motor, or stretch a leg, and it falls over. I built **FAGT**, a transformer policy that reads the robot's kinematic graph and URDF parameters as explicit inputs, so one network can control a whole family of Unitree G1 variants.

- **Faster training:** reaches the 1000-step episode ceiling in **~300 iterations**. The baseline plateaus at **~870 steps** after ~410 iterations.
- **Better zero-shot transfer:** on unseen robots, mean episode length goes from **248 → 361 (+46%)**. FAGT wins on **6/6** perturbation test sets.
- **Faster adaptation out of distribution:** after only 50 fine-tuning iterations on much harder robots, the mean is **548 vs 308 steps (+240)**.
- **Interpretable structure:** FAGT's link embeddings cluster cleanly by **hip / knee / ankle across every robot variant**, without ever being given joint labels.

---

## The Problem

RL policies learn a mapping from observations to actions that silently assumes one fixed body. Real robots don't stay fixed:

- **Damage and wear.** A degraded motor or a cracked link changes effective mass, damping and actuator authority.
- **Payloads.** An asymmetric load or protective gear shifts the mass distribution.
- **Fleets.** Two "identical" robots differ in manufacturing tolerances, and new hardware revisions change proportions.

Retraining from scratch for every change doesn't scale. The goal is a policy that is **conditioned on the morphology**: you tell it what body it is in (the URDF is already known), and it adapts its gait.

---

## Where Prior Work Stands

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/fagt_metamorph_unimal.png" title="UNIMAL agents used by MetaMorph" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    UNIMAL agents from <a href="https://arxiv.org/abs/2203.11931">MetaMorph</a> (Gupta et al., ICLR 2022). MetaMorph showed that one transformer can control 100+ different morphologies. Each limb becomes a token, per-limb morphology context is <em>concatenated</em> onto the observation, and self-attention coordinates the limbs. It is the main reference point for this work, and my baseline follows the same pattern. But look at the agents: they are built from near-identical capsule modules, so every limb plays a similar role. A humanoid is the opposite case. The pelvis, hips, knees and ankles differ in mass by up to two orders of magnitude and do very different biomechanical jobs.
</div>

Morphology-agnostic RL has gone through roughly three waves:

1. **GNN message passing** (NerveNet, Shared Modular Policies). These share one policy across a robot graph, but multi-hop message passing struggles to coordinate distant limbs.
2. **Transformers over limb tokens** (Amorpheus, MetaMorph, ModuMorph). Attention gives every limb direct access to every other limb. Structure is either ignored or injected as simple positional embeddings or concatenated context.
3. **Morphology-aware transformers** (SWAT, Body Transformer, GCNT, HeteroMorpheus). These add structural priors back on top of attention.

Three gaps remained, and they motivated this thesis:

- **Weak structural priors.** Limb identity is usually a one-hot index or a tree-traversal order. A one-hot "left knee" has no relationship to "right knee", so the policy has to relearn from scratch that the two play analogous roles.
- **Weak context integration.** Concatenating context with observations only lets the context act as an additive bias (shown below). Hypernetworks (ModuMorph) are more expressive but are known to be hard to optimize.
- **No humanoids.** Nearly all of this work is validated on UNIMAL-style homogeneous agents, not bipeds that need asymmetric, per-side compensation.

---

## The Inspiration: FiLM for Embodiment

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/fagt_vla_film_inspiration.png" title="Embodiment-aware transformer for cross-robot VLA policies" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    From <em>Embedding Morphology into Transformers for Cross-Robot Policy Learning</em> (Suzuki et al.). In this vision-language-action policy for manipulation, the action expert receives the robot's kinematic topology and per-joint descriptors (joint type, axis, limits). The descriptors condition the joint tokens through FiLM. This paper is what pointed me towards FiLM. It is the closest architectural neighbour to my work, but it targets manipulation, uses a hand-crafted topology attention bias, and is not evaluated on humanoid locomotion or asymmetric physical perturbations.
</div>

**Why not just concatenate the context?** If the proprioceptive features $$o_i$$ and the morphology context $$c_i$$ are concatenated and passed through a linear layer:

$$
W \begin{bmatrix} o_i \\ c_i \end{bmatrix} = W_o\, o_i + W_c\, c_i
$$

The context term is **purely additive**. It can shift the features, but it cannot re-scale them, turn channels up or down, or change which features matter for a given robot. **FiLM** (Feature-wise Linear Modulation) restores that multiplicative interaction:

$$
\tilde{h}_i = \gamma(c_i) \odot h_i + \beta(c_i)
$$

FiLM and attention are complementary. **Attention** decides *which limbs* a limb reads from (mixing across tokens). **FiLM** decides *how each limb's features are weighted* given what that limb physically is (mixing along the feature axis).

---

## The Architecture: FiLM-Aided Graph Transformer (FAGT)

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/fagt_architecture.png" title="FAGT architecture" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    FAGT overview. Per-link proprioception is enriched with structural embeddings from a GCN over the robot graph (RWSE features). The result is FiLM-modulated by a per-link context vector parsed from the URDF and fed as the <em>values</em> into a transformer whose <em>queries and keys</em> come from the morphology context. One shared network outputs a target position for every joint across all robot variants.
</div>

FAGT injects morphology through three complementary channels. Each one answers a different question:

| Component | Answers | Input |
|---|---|---|
| GCN over RWSE | *Where* does this limb sit in the kinematic tree? | Robot graph |
| Context vector + FiLM | *What* is this limb physically? | URDF |
| Context-driven attention | *Which* limbs should talk to each other? | URDF |

### 1. Per-link tokens

The robot is treated as a set of links (zero-padded and masked so that variants with different sizes can share a batch). Each link $$i$$ gets two vectors:

- **Proprioceptive state $$o_i \in \mathbb{R}^{26}$$** (changes every step). It holds the link's root-relative position, linear and angular velocity, orientation (exponential map), normalized joint angle and joint limits. Global state (base angular velocity, projected gravity, velocity command, gait phase) is attached only to the root link.
- **Morphology context $$c_i \in \mathbb{R}^{12}$$** (fixed per robot). It is parsed once from the URDF and holds link mass, parent–child offset (a proxy for link length), CoM height, joint limits, actuator effort, damping and rotation axis. Normalization uses **fixed ranges shared across all variants**, so a heavier robot actually *looks* heavier to the policy instead of being standardized away.

### 2. Structural encoding with RWSE + GCN

To give every limb a sense of its position in the body, the kinematic tree is encoded as a graph. Each node's features are its **Random Walk Structural Encoding** (RWSE), the probability that a random walk returns to that node after $$t$$ steps:

$$
x_v^{(t)} = \left[ R^t \right]_{vv}, \quad t = 1, \dots, 8, \qquad R = D^{-1}(A + I)
$$

This signature captures degree, branching and depth purely from connectivity, with **no hand-labelled limb types**. A lightweight two-layer GCN processes it, and the output is **added** to the proprioceptive embedding as a structural offset:

$$
h_i = W_e\, o_i + W_p\, g_i
$$

RWSE is standard in graph transformers (GraphGPS), but to my knowledge it had not been used in robot policies, which have relied on one-hot IDs or tree-traversal orderings.

### 3. FiLM conditioning on morphology

A small MLP maps each link's context $$c_i$$ to per-channel $$(\gamma_i, \beta_i)$$, which modulate the GCN-enriched token: $$\tilde{h}_i = \gamma_i \odot h_i + \beta_i$$.

**Identity initialization:** the generator's output layer starts with zero weights and biases $$(1, 0)$$, so at step 0, $$\gamma \equiv 1$$ and $$\beta \equiv 0$$. FAGT therefore starts out exactly like an unmodulated policy, and morphology-specific modulation is learned on top of a stable starting point. This avoids the initialization sensitivity that makes hypernetworks hard to train.

### 4. Transformer with context-driven attention

Attention *routing* is decoupled from attention *content*:

$$
Q = \tilde{C} W_Q, \quad K = \tilde{C} W_K, \quad V = \tilde{H} W_V
$$

Queries and keys come from the time-invariant morphology context. Values carry the fast-changing, FiLM-modulated proprioceptive signal. The intuition is that the ankle always needs to know what the pelvis is doing, whether the leg is in swing or stance. "Who talks to whom" is a property of the body, not of the current sensor readings. This gives the network a stable communication map, so it can focus on *how* to move.

---

## Experimental Setup

- **Simulator:** NVIDIA Isaac Gym, built on [unitree_rl_gym](https://github.com/unitreerobotics/unitree_rl_gym) / legged_gym. I extended it to load multiple URDF variants into one vectorized environment, each with its own graph tensors.
- **Task:** forward velocity tracking on flat terrain ($$v_x \sim \mathcal{U}(0.5, 1.0)$$ m/s), with PD position control and PPO. An episode ends on any non-foot ground contact or at 1000 steps.
- **Robot variants:** generated from the Unitree G1 URDF by randomly scaling **link masses, link lengths (joint origins), actuator effort, joint damping and armature**. The topology stays fixed, so the challenge is isolated to *parametric* morphology: 10 training variants plus disjoint held-out sets.
- **Metric:** episode length (cap 1000). It measures survival and gait stability directly, while episode return occupies a narrow, insensitive range on this task.
- **Baseline: Context-Transformer (CT).** Each limb token is `MLP([o_i ; c_i])` (context concatenated, as in MetaMorph/ModuMorph), passed through a standard transformer. It has no GCN/RWSE, no FiLM and no context-driven attention, and uses the same codebase, hyperparameters and compute budget. Direct ModuMorph/MetaMorph comparisons weren't meaningful because both are built around the UNIMAL agent space.
- 5 seeds per experiment, on a single NVIDIA L40S.

### Finding the discriminative difficulty window

Each variant gets a difficulty score, the mean absolute deviation of its scale factors from the base G1:

$$
\delta_v = \frac{1}{|\mathcal{P}|} \sum_{p \in \mathcal{P}} \left| 1 - s_{v,p} \right|
$$

A pilot run showed three regimes:

- **$$\delta_v \lesssim 0.22$$:** both methods saturate at 1000 steps, so the variants are too easy to tell the methods apart.
- **$$0.22 \le \delta_v \le 0.40$$:** the baseline drops to as low as ~300 steps while FAGT stays within ~200 steps of the ceiling. This is the **discriminative window**.
- **$$\delta_v \gtrsim 0.40$$:** both start to degrade.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/fagt_difficulty_window.png" title="Converged episode length vs morphology difficulty" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Converged episode length after training vs. morphology difficulty score δ<sub>v</sub>, for FAGT (blue) and the Context-Transformer baseline (orange). Below δ<sub>v</sub> ≈ 0.22 both methods hit the 1000-step ceiling. Inside the shaded window [0.22, 0.40], FAGT stays near the ceiling while the baseline drops to ~300–450 steps on the hardest variants.
</div>

All training and zero-shot experiments use variants from this window. The few-shot experiments deliberately go beyond it.

---

## Results

### Training efficiency

On identical variant sets and hyperparameters, **FAGT reaches the 1000-step ceiling in ~300 iterations. The baseline peaks at ~870 steps after ~410 iterations.** Within the first quarter of training, FAGT already beats the baseline's final plateau on several of the harder variants.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/fagt_training_curve.png" title="Training curves for FAGT and the baseline" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Average episode length across all training variants vs. PPO iteration. FAGT (blue) reaches the 1000-step ceiling at ~300 iterations. The Context-Transformer baseline (red) peaks at 870 steps after ~410 iterations and never reaches the ceiling. The two curves separate around iteration 190, which is the point where the baseline stalls.
</div>

The baseline isn't incapable of walking. It has to discover limb roles and morphology effects from data, and FAGT is *given* them. That makes the gain one of **compute and sample efficiency**.

### Zero-shot generalization to unseen robots

Six held-out test sets were used: five perturb a single property, and one perturbs all five at once. Each set holds 10 unseen variants from the same difficulty window, generated with a different random seed from the training set. Policies are evaluated with no further training (100 episodes per variant). The numbers are the top-10% episode length (mean ± 95% CI):

| Test set | Context-Transformer | **FAGT** | Δ |
|---|---|---|---|
| Mass | 245.8 ± 37.8 | **354.4 ± 61.0** | +109 |
| Joint range | 251.7 ± 34.7 | **369.8 ± 73.5** | +118 |
| Actuator effort | 249.7 ± 40.5 | **377.2 ± 79.6** | +128 |
| Damping | 259.7 ± 43.5 | **353.6 ± 72.4** | +94 |
| Armature | 236.4 ± 46.9 | **369.6 ± 67.8** | +133 |
| All perturbed | 245.2 ± 26.7 | **342.7 ± 58.6** | +98 |
| **Mean** | 248.1 | **361.2** | **+113 (+46%)** |

FAGT wins on **all six** sets. The largest margins are on armature (+133), effort (+128) and joint range (+118), and the smallest, though still clearly positive, is on damping (+94). Individual confidence intervals overlap because of the small number of held-out robots per set, but the probability of winning 6/6 under a no-difference null is $$(1/2)^6 \approx 0.016$$. As a sanity check, a **flat MLP policy with no context or structure survives only ~60 steps** on every set. Context conditioning is essential, and *how* it is injected is what separates FAGT from CT.

### Does the graph encoding actually learn structure?

I ran t-SNE on the 128-d tokens entering the transformer, collected across all training variants and coloured by joint group (hip / knee / ankle). The policy never sees these labels.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/fagt_tsne.png" title="t-SNE of per-link transformer-input tokens" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    t-SNE of per-link transformer-input tokens (128-d → 2D). Colour shows joint group; marker shape shows which robot variant the token came from. <b>Left:</b> Context-Transformer baseline, where hip, knee and ankle tokens are intermixed. <b>Right:</b> FAGT, where the joint groups separate cleanly. Hip and ankle tokens form a few sub-clusters, but no cluster mixes groups, and markers from every robot variant fall inside the same clusters.
</div>

- **Context-Transformer:** hip, knee and ankle tokens are intermixed. Functional identity is entangled with per-robot parameters.
- **FAGT:** the tokens **separate cleanly by joint group**, and the same joint type from *different robot variants* lands in the same cluster.

This is direct evidence that the RWSE + GCN pathway provides a robot-agnostic structural prior. The policy knows that a left ankle on variant 3 plays the same role as a left ankle on variant 7.

### Few-shot adaptation to out-of-distribution robots

Five much harder robots were built with **compounding asymmetric perturbations**, for example heavy links paired with weak actuators and restricted joint range, all on one side ($$\delta_v \in [0.52, 0.59]$$, well outside the training window). Both methods start from their 400-iteration checkpoints and get the **same 50-iteration PPO budget** on each robot. Any gap therefore reflects the quality of the prior each method learned.

| Robot | δ | **FAGT** | Context-Transformer | Δ |
|---|---|---|---|---|
| Base G1 | 0.00 | 952 | **1001** | −49 |
| OOD 0 | 0.52 | **670** | 595 | +75 |
| OOD 1 | 0.57 | **580** | 154 | **+426** |
| OOD 2 | 0.54 | **728** | 210 | **+518** |
| OOD 3 | 0.59 | 276 | **323** | −47 |
| OOD 4 | 0.52 | **487** | 260 | +227 |
| **Mean (OOD)** | — | **548** | 308 | **+240** |

FAGT wins on 4 of 5 OOD robots, and the gap is largest on the hardest transfers. The baseline collapses on asymmetric bodies, where per-side compensation matters most.

### A single rollout, end to end

On one held-out G1 variant with all five properties perturbed:

- the **zero-shot baseline** falls at **~340 steps**
- **zero-shot FAGT** walks for **655 steps** with no extra training
- **FAGT after 100 fine-tuning iterations** walks the **full 1000-step horizon**

---

## Takeaways

- **Explicit priors break up the problem.** An attention-only policy has to work out *what role each limb plays* and *how to act on it* at the same time, which becomes intractable once morphological variation is large. In FAGT, the GCN handles roles, FiLM handles physical adaptation, and the transformer focuses on producing actions.
- **Humanoids are where this matters.** On homogeneous UNIMAL-style agents, the advantage would likely be smaller. The heterogeneous, asymmetric nature of humanoid bodies is what gives explicit structure its leverage.
- **This is not domain randomization.** Domain randomization hides the parameters and learns an averaged gait. Morphology conditioning *uses* the known URDF, so it can deliberately compensate on the weaker side.

## Limitations & Future Work

- **Fixed topology.** All variants share the G1 kinematic tree. The architecture is topology-agnostic (variable-length tokens, arbitrary graphs, RWSE), but cross-platform transfer (e.g. G1 → H1) also needs platform-agnostic rewards and observation normalization.
- **Scale.** 10 training variants is enough to show the architectural effect. MetaMorph-style zero-shot transfer probably needs many more.
- **Simulation only.** The natural next step is **sim-to-real via system identification**: measure the physical robot's parameters, feed them in as context, and let the policy adapt.
- **Distillation.** Distilling the generalist into lightweight per-robot specialists would make it practical to run on embedded hardware.

For the full method, derivations, reward design and hyperparameters, see the [thesis PDF](https://drive.google.com/file/d/1DMo-htU065Fb84vncxe2e-Iy5C9iW3cI/view?usp=sharing).
