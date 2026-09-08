+++
title = "Multi-Target Tracking via Field-Based Distributed Particle Filtering"
description = "ACSOS 2026 presentation"
outputs = ["Reveal"]
+++

# Multi-Target Tracking via Field-Based Distributed Particle Filtering

{{% spacer %}}

[**<span class="deck-title-accent">Angela Cortecchia</span>**](mailto:angela.cortecchia@unibo.it),
[Davide Domini](mailto:davide.domini@unibo.it),
[Giovanni Ciatto](mailto:giovanni.ciatto@unibo.it),
[Roberto Casadei](mailto:roby.casadei@unibo.it), and
[Mirko Viroli](mailto:mirko.viroli@unibo.it)

{{% spacer %}}

<span class="deck-affiliation">*Department of Computer Science and Engineering (DISI)<br>
Alma Mater Studiorum — University of Bologna, Cesena, Italy*</span>

<div class="hero-logo">
  <img src="./images/DIP INFORMATICA-SCIENZA E INGEGNERIA_DISI_EN.svg" alt="University of Bologna — DISI">
</div>

---

# Motivation: distributed estimation in changing environments

{{< deck-grid columns="2" class="motivation-grid" >}}
{{< deck-panel label="WHY IT MATTERS" title="Distributed evidence, changing infrastructure" >}}
Many cyber-physical systems estimate a **hidden dynamical state** from distributed, noisy observations.

But the sensing system changes too:

- devices move, fail, or disconnect;
- topology and observability vary;
- different devices become relevant over time.
  {{< /deck-panel >}}

<img alt="Gemini_Generated_Image_y0tp7fy0tp7fy0tp.jpg" src="images/Gemini_Generated_Image_y0tp7fy0tp7fy0tp.jpg" width="80%"/>

{{< /deck-grid >}}

{{< meta-note >}}
**Challenge:** keep the estimate reliable while the organization producing it changes.
{{< /meta-note >}}

---

# Particle filters maintain a distribution of plausible states

{{< deck-grid columns="2" class="pf-intro-grid" >}}
{{< deck-panel label="FILTERING LOOP" title="Four operations update the belief" class="process-panel" >}}
**01 · Predict** — Where could the state move next?

**02 · Weight** — Which hypotheses agree with the observations?

**03 · Resample** — Keep plausible hypotheses; discard unlikely ones.

**04 · Estimate** — Use the particles to represent the current belief.
{{< /deck-panel >}}

<div class="pf-visual">
<img src="./images/particles-distribution/step_0.png">
<p><strong>The estimate is a distribution, not only a point.</strong></p>
</div>

{{< /deck-grid >}}

---

# Particle Filter Over Time

<div class="image-grid">
  <div class="image-grid-item">
    <img src="./images/particles-distribution/step_0.png">
    <div class="image-grid-title">t = 0</div>
    <div class="image-grid-caption">many possible hypotheses</div>
  </div>
  <div class="image-grid-item">
    <img src="./images/particles-distribution/step_1000.png">
    <div class="image-grid-title">t = 1000</div>
    <div class="image-grid-caption">belief concentrates</div>
  </div>
  <div class="image-grid-item">
    <img src="./images/particles-distribution/step_2000.png">
    <div class="image-grid-title">t = 2000</div>
    <div class="image-grid-caption">the cloud follows the target</div>
  </div>
  <div class="image-grid-item">
    <img src="./images/particles-distribution/step_2900.png">
    <div class="image-grid-title">t = 2900</div>
    <div class="image-grid-caption">uncertainty remains explicit</div>
  </div>
</div>

{{< meta-note >}}
The particle cloud follows the target while keeping the estimation error visible as uncertainty.
{{< /meta-note >}}

---

# Distribution turns filtering into a coordination problem

{{< deck-grid columns="2" class="comparison-grid" >}}
{{< deck-panel label="ONE ESTIMATOR" title="Centralized particle filtering" >}}
All observations are available to a single estimator.

$$p(x_t \mid y_{1:t})$$
{{< /deck-panel >}}

{{< deck-panel label="MANY DEVICES" title="Distributed particle filtering" tone="red" >}}
Each device observes only local information.

$$y_{t,k} = h_k(x_t, v_{t,k})$$
{{< /deck-panel >}}
{{< /deck-grid >}}

Different DPF approaches mainly differ in **where fusion happens, what is exchanged, how far information propagates, and which nodes participate**.

{{< pf-taxonomy-tree >}}

{{< meta-note >}}
The filtering objective stays the same; the coordination strategy changes.
{{< /meta-note >}}

---

# Conventional DPF architectures are rigid by design

{{< deck-grid columns="2" class="comparison-grid" >}}
{{< deck-panel label="DESIGN TIME" title="Fixed architecture" >}}
Fusion centers, leaders, communication patterns, and participating nodes are typically defined in advance.
{{< /deck-panel >}}

{{< deck-panel label="RUNTIME" title="Limited adaptation" tone="red" >}}
Failures, mobility, topology changes, or shifting observability often require specialized algorithm variants.
{{< /deck-panel >}}
{{< /deck-grid >}}

{{< meta-note >}}
**Core issue:** filtering semantics and coordination mechanisms are tightly coupled.
{{< /meta-note >}}

---

# Aggregate Computing makes collective coordination programmable

### A macroprogramming approach to distributed systems

{{< deck-grid columns="2" class="ac-overview-grid" >}}
{{< deck-figure src="./images/acDevices.svg" alt="Aggregate Computing devices forming a computational field" class="ac-figure" >}}

{{< deck-panel label="ONE PROGRAM, MANY DEVICES" title="Reason globally; execute locally" >}}
Aggregate Computing describes the **collective behavior** of a system instead of programming each device separately.

Programs manipulate **computational fields**: distributed values that evolve across the network.

<div class="keyword-line">
  <span>spread</span><span>aggregate</span><span>converge</span><span>elect</span>
</div>
{{< /deck-panel >}}
{{< /deck-grid >}}

{{< meta-note >}}
Devices execute local code, while the programmer reasons in terms of global coordination patterns.
{{< /meta-note >}}

---

# One collective program becomes an evolving computational field

{{< deck-grid columns="2" class="model-grid" >}}
{{< deck-panel label="COLLECTIVE VIEW" title="From specification to field" class="numbered-panel" >}}
**01 · Collective specification**<br>
The programmer describes the behavior of the device network.

**02 · Local rounds**<br>
Each device evaluates the same program using sensors, memory, and neighbor messages.

**03 · Emergent field**<br>
The collection of local values forms the global computational field.
{{< /deck-panel >}}

{{< deck-panel label="DEVICE VIEW" title="Every round repeats three phases" class="round-panel" >}}
1. **Sense** inputs and incoming neighbor messages.
2. **Compute** new local state and outbound messages.
3. **Interact / Act** by sharing results and affecting the environment.

{{< local-round-loop >}}

The model does not require global lockstep.
{{< /deck-panel >}}
{{< /deck-grid >}}

---

# Keep particle filtering standard; make coordination adaptable

<p class="lead-line">This is not another DPF algorithm. It is a programmable coordination layer around established filtering logic.</p>

{{< deck-grid columns="2" class="idea-grid" >}}
{{< deck-panel label="STABLE" title="Filtering logic" >}}
- prediction
- weighting
- resampling
- estimation
{{< /deck-panel >}}

{{< deck-panel label="PROGRAMMABLE" title="Coordination choices" tone="red" >}}
- where information is fused;
- what information is exchanged;
- how far information propagates;
- which devices participate;
- which devices take coordination roles.
{{< /deck-panel >}}
{{< /deck-grid >}}

{{< meta-note >}}
Architectural assumptions become composable choices that can evolve at runtime.
{{< /meta-note >}}

---

# One field-based model supports multiple adaptive organizations

{{< deck-grid columns="2" class="contribution-grid" >}}
{{< deck-panel label="CONTRIBUTION 01" title="DPF as a self-organizing collective process" >}}
- **Self-organization** through local neighborhood interactions
- **Self-healing** through runtime role reassignment after failures
- **Self-adaptation** to target motion and changing spatial conditions
{{< /deck-panel >}}

{{< deck-panel label="CONTRIBUTION 02" title="One model, multiple DPF organizations" tone="red" >}}
- neighborhood-level measurement aggregation
- leader-based fusion with dynamic leader election
- decentralized estimation with fixed or mobile observers
- adaptive spatial participation and coordination regions
{{< /deck-panel >}}
{{< /deck-grid >}}

{{< meta-note >}}
The filtering logic remains constant; the collective reorganizes around it.
{{< /meta-note >}}

---

# Experimental Evaluation

<div class="eval-layout">
  <div class="eval-main">
    <h3>Scenario</h3>
    <div class="metric-band">
      <span class="metric-pill">Alchemist Simulator</span>
      <span class="metric-pill">Collektive DSL</span>
      <span class="metric-pill">2D environment</span>
      <span class="metric-pill">24 sensors</span>
      <span class="metric-pill">3 moving targets</span>
      <span class="metric-pill">50 seeds · 1 Hz</span>
    </div>
    <p>Target trajectories derive from the <strong>KABR wildlife telemetry dataset</strong>.</p>
    <p>Each target emits a radio-like signal: closer sensors receive stronger, cleaner evidence; farther sensors receive weaker, noisier evidence.</p>
  </div>
  <div class="eval-configs">
    <h3>Evaluated configurations</h3>
    <div class="config-row">
      <span class="config-index">1</span>
      <div><strong>Neighborhood measurement aggregation</strong><br>Each sensor runs a local PF; only measurements are shared.</div>
    </div>
    <div class="config-row is-critical">
      <span class="config-index">2</span>
      <div><strong>Leader-based fusion</strong><br>Measurements converge toward an elected, replaceable leader.</div>
    </div>
    <div class="config-row">
      <span class="config-index">3</span>
      <div><strong>Mobile sensing infrastructure</strong><br>Sensors reorganize spatially around the moving targets.</div>
    </div>
  </div>
</div>

<img alt="Experimental setup" src="./images/dpf-cropped.gif" class="eval-gif">

---

# Experiment 1: neighborhood sharing improves local estimates

{{< deck-grid columns="3" class="experiment-setup-grid" >}}
{{< deck-panel label="MECHANISM" title="Local filters, shared evidence" >}}
Each sensor runs its **own multi-target particle filter**.

Sensors exchange target-indexed measurements—not particle sets—and combine neighbor observations in the likelihood computation.

$$|\mathcal{N}| \in \{0, 1, 4, 7\}$$
{{< /deck-panel >}}

{{< deck-panel label="FEW NEIGHBORS" title="Sparse evidence" tone="red" >}}
Limited spatial information produces poor convergence and unstable estimates.
{{< /deck-panel >}}

{{< deck-panel label="MORE NEIGHBORS" title="Richer evidence" >}}
Aggregated measurements improve tracking accuracy and stability.
{{< /deck-panel >}}
{{< /deck-grid >}}

{{< deck-figure src="./images/three_zebras_by_number_of_neighbors.png" alt="Multi-target trajectories for different neighborhood sizes" class="wide-results-figure" >}}

{{< meta-note >}}
Neighborhood size becomes an explicit coordination parameter.
{{< /meta-note >}}

---

# Measurement sharing sharply reduces tracking error

{{< deck-grid columns="2" class="results-grid" >}}
{{< deck-figure src="./images/RMSE.png" alt="RMSE results for different neighborhood sizes" caption="RMSE by neighborhood size across the simulation campaign." class="results-figure" >}}

{{< deck-panel label="RESULT" title="Cooperation improves accuracy" >}}
- With little or no sharing, local filters may remain inaccurate or fail to converge.
- Increasing $|\mathcal{N}|$ reduces RMSE and improves stability.
- Particle sets remain local; only measurements are exchanged.
- Larger neighborhoods trade communication for estimation quality.
{{< /deck-panel >}}
{{< /deck-grid >}}

{{< meta-note >}}
Local cooperation supports decentralized multi-target tracking without a fusion center.
{{< /meta-note >}}

---

# Experiment 2: the fusion role survives leader failure

{{< deck-grid columns="2" class="experiment-grid" >}}
{{< deck-panel label="SELF-HEALING SEQUENCE" title="Elect, fuse, fail, recover" class="numbered-panel" >}}
**01 · Election**<br>
A geometrically central active sensor becomes leader.

**02 · Fusion**<br>
Measurements converge toward the leader.

**03 · Failure**<br>
The current leader is removed during execution.

**04 · Recovery**<br>
A new leader is elected; tracking resumes after a short transient.
{{< /deck-panel >}}

{{< deck-figure src="./images/three_zebras_fc_failure.png" alt="Leader-based tracking before and after fusion-center failure" class="experiment-figure" >}}
{{< /deck-grid >}}

{{< meta-note >}}
The fusion-center role persists even when the device holding that role does not.
{{< /meta-note >}}

---

# Experiment 3: mobile sensors preserve informative observations

{{< deck-grid columns="2" class="experiment-grid mobile-grid" >}}
{{< deck-panel label="SELF-ADAPTIVE MOBILITY" title="The infrastructure follows the targets" >}}
- Every sensor continues to run a local multi-target particle filter.
- Filtering remains decentralized.
- A temporary coordinator manages motion only.
- Sensors preserve a grid-like formation.
- The formation centroid follows the estimated target centroid.

Observation quality then depends on how well the formation keeps up.
{{< /deck-panel >}}

{{< deck-figure src="./images/sensors_zebras_movement.png" alt="Mobile sensors reorganizing around three moving targets" class="mobile-figure" >}}
{{< /deck-grid >}}

---

# Tracking accuracy bounds estimation accuracy

{{< deck-grid columns="2" class="results-grid" >}}
{{< deck-figure src="./images/rmse_by_error_on_position.png" alt="RMSE as a function of the tracking error of the moving sensors" caption="Estimation error vs. tracking error of the mobile sensors." class="results-figure tracking-error-figure" >}}

{{< deck-panel label="RESULT" title="Mobility helps only while the formation keeps up" >}}
- **Tracking error**: how far the sensors stay from the position they follow.
- RMSE grows **super-linearly**: 2 m at 0, ~26 m at 200, ~59 m at 300.
- Sensors that lag behind receive a weaker, noisier signal.
{{< /deck-panel >}}
{{< /deck-grid >}}

{{< meta-note >}}
Mobility buys accuracy only in proportion to how well the collective keeps up.
{{< /meta-note >}}

---

# The same formulation enables three self-* properties

{{< deck-grid columns="3" class="outcome-grid" >}}
{{< deck-panel label="01" title="Self-organization" >}}
Neighborhood interactions improve estimation without central coordination.
{{< /deck-panel >}}

{{< deck-panel label="02" title="Self-healing" tone="red" >}}
A failed fusion leader is replaced at runtime while the collective behavior persists.
{{< /deck-panel >}}

{{< deck-panel label="03" title="Self-adaptation" >}}
Mobile observers reorganize as the tracked targets move.
{{< /deck-panel >}}
{{< /deck-grid >}}

{{< meta-note >}}
The key result is not one DPF configuration, but multiple adaptive organizations emerging from one field-based formulation.
{{< /meta-note >}}

---

# Field-based DPF separates estimation from coordination

{{< deck-grid columns="2" class="closing-grid" >}}
{{< deck-panel label="TAKEAWAYS" title="What the work establishes" >}}
- DPF becomes a **self-organizing collective process**.
- One estimation substrate supports multiple organizations.
- Local cooperation, leader replacement, and mobile observers expose three self-* properties.
- Neighborhood size, connectivity, and spatial configuration shape estimation quality.
{{< /deck-panel >}}

{{< deck-panel label="NEXT" title="Future work" tone="red" >}}
- spatially adaptive filtering and selective activation
- self-organizing coordination regions
- larger target populations
- richer deployment conditions
- heterogeneous sensing modalities
- accuracy, communication, resilience, and scalability trade-offs
{{< /deck-panel >}}
{{< /deck-grid >}}

---

# Thank you for the attention!

{{% spacer %}}

### Reproducible experiments here:

{{< deck-figure src="./images/qrcode.png" alt="QR code for the experiment repository" class="qr-figure" >}}

GitHub: <i class="fa-brands fa-github"></i> **[domm99/experiments-acsos-2026-DPF-multi-object-tracking](https://github.com/domm99/experiments-acsos-2026-DPF-multi-object-tracking)**
