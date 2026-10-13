<!-- solve-problem:41ac2045-cc14-4053-836a-8a8fc425f8ea -->
# Withholding under correlated reward shocks

A [Tangle Solve problem](https://solve.tangle.tools/problems/41ac2045-cc14-4053-836a-8a8fc425f8ea).

## What is the problem?

Consider the standard selfish-mining model: a miner with hash fraction α mines on a private chain, selectively withholding blocks, and publishes to orphan honest blocks. Classically (Eyal–Sirer) each block carries a fixed reward. Here, each block carries a reward drawn from a stochastic process: transaction fees arrive in **correlated bursts** (fee spikes cluster), rewards for a block depend on the **mempool state at the time it would be mined**, and crucially the attacker's private mempool differs from the public one — so the reward of a withheld block is not the reward the honest chain would have assigned it, and it decays/changes the longer the block is withheld.

Precise formulation sought: let R_t be a reward process (e.g., a Markov-modulated or AR-like process with positive autocorrelation, capturing fee bursts). A withholding policy π chooses, at each state of the private-lead Markov chain (with parameter γ of network propagation), whether to publish or continue withholding. Withholding for k extra rounds changes expected reward via (i) correlation decay of the burst, (ii) staleness of the captured fees, (iii) the private-mempool information asymmetry. **Goal:** characterize the optimal policy π* maximizing long-run average (or discounted) per-round reward, and the resulting profitability threshold α*(γ, correlation parameters). Ambiguity: the census does not fix the reward process family or the mempool asymmetry model — a solver must choose and justify a tractable formalization.

## Why does it matter?

As block subsidies halve toward zero, transaction fees dominate miner revenue, so selfish-mining profitability under realistic fee dynamics — not fixed rewards — determines whether Bitcoin's incentive compatibility actually holds post-subsidy. A solution gives protocol designers the correct fee-market design levers (fee smoothing, encumbered fees) needed to keep withholding unprofitable. It also directly informs how exchanges and users should reason about reorg risk during fee spikes (e.g., after BRC-20-style congestion events).

## What's already known?

- Classical fixed-reward selfish mining: Eyal–Sirer (2013), Sapirshtein et al. (2016) — optimal withholding computed via MDP on the private-lead state; threshold α* ≈ 0.25 (γ = 0) to 0 (γ = 1).
- Bahrani, Neuder & Weinberg (2025, arXiv:2502.20360) extend selfish mining to **general stochastic rewards** where the attacker observes reward realizations and can condition withholding on them; they characterize optimal policies and show stochastic rewards shift attack thresholds materially. This is the direct baseline — but it treats rewards (essentially) as i.i.d./independently observable per block.
- Sarenche et al. (2025, arXiv:2411.11702) show empirically/analytically that volatile, mempool-dependent block rewards change selfish-mining incentives, motivating richer mempool modeling.
- Open gap: no published treatment handles (a) temporally **correlated** rewards (bursts), (b) reward **decay with withholding delay**, (c) **private-mempool heterogeneity** (attacker's view ≠ public view), or (d) robustness to model uncertainty in the reward process.

## What would count as solving it?

1. **Optimal policy characterization:** A theorem giving the structure of π* (e.g., threshold policies on private lead × current fee level) for a specified correlated reward process (Markov-modulated bursts), with proof via MDP/POMDP analysis.
2. **Threshold formula/bound:** Closed-form or tight bounds on α*(γ, ρ, δ) where ρ is burst correlation and δ the private-mempool divergence parameter, recovering Bahrani–Neuder–Weinberg as ρ, δ → 0.
3. **Robust variant:** A policy maximizing worst-case reward over an uncertainty set of reward processes, with a regret guarantee.
4. **Numerical validation:** Calibrated simulation on mempool data showing the model's thresholds match observed attack profitability under bursty fees.

## How to start

1. Read arXiv:2502.20360 first — master its MDP formulation and how stochastic rewards enter the state; your model is its extension with an augmented state (current burst level, mempool divergence).
2. Formalize the reward process: a two-state Markov chain (burst/normal) times a fee level is the minimal model capturing correlation; solve the finite-state MDP exactly to get structural intuition (are optimal policies still threshold-on-lead?).
3. Add delay decay by making a withheld block's reward a function of (fee level at mining, fee level at release) — this turns the problem into a partially observed one; POMDP tools or belief-state relaxation apply.
4. Main obstacle: private-mempool asymmetry makes the attacker's reward unobservable-in-principle to honest miners and creates a belief state; the known obstacle in this literature is that correlated rewards break the renewal/Markov structure that makes classical proofs work.

## Key references

- Bahrani, M., Neuder, A., Weinberg, S. M. (2025). *Selfish Mining under General Stochastic Rewards*, arXiv:2502.20360. Extends the selfish-mining MDP to block rewards drawn from a general stochastic process observable to the attacker, deriving optimal withholding policies and revised profitability thresholds. https://arxiv.org/abs/2502.20360
- Sarenche et al. (2025). *Bitcoin under Volatile Block Rewards: How Mempool Statistics Can Influence Bitcoin Mining*, arXiv:2411.11702. Analyzes how mempool-driven block-reward volatility alters mining-strategy incentives, providing empirical motivation for correlated-fee modeling. https://arxiv.org/abs/2411.11702
- Sapirshtein, A., Sompolinsky, Y., Zohar, A. (2016). *Optimal Selfish Mining Strategies in Bitcoin*. Computes optimal selfish mining via MDP over the private-lead state space — the baseline machinery. (Standard reference; verify edition.)
- Eyal, I., Sirer, E. G. (2014). *Majority is not Enough: Bitcoin Mining is Vulnerable*. The original selfish-mining result with fixed rewards, framing the threshold problem.

## Warnings

- The census record is a research-target description, not a precise problem statement; the reward-process and mempool-asymmetry models are yours to define — state them explicitly and check recoverability of the 2025 baseline as a sanity check.
- Do not assume the attacker observes honest miners' mempool; that assumption trivializes the private-mempool component.
- Correlated rewards break the regenerative structure used in fixed-reward proofs; naive adaptation of Eyal–Sirer/Sapirshtein arguments will fail.
- Average-fee calibration hides the economic effect — results must be stated in terms of burst/correlation parameters, not means.
- Verify the exact scope of the two 2025 preprints before claiming novelty for any component; the field is moving quickly.

---
*`BCGT-000006` · flagged for review*

## Acceptance guidance

1. **Optimal policy characterization:** A theorem giving the structure of π* (e.g., threshold policies on private lead × current fee level) for a specified correlated reward process (Markov-modulated bursts), with proof via MDP/POMDP analysis.
2. **Threshold formula/bound:** Closed-form or tight bounds on α*(γ, ρ, δ) where ρ is burst correlation and δ the private-mempool divergence parameter, recovering Bahrani–Neuder–Weinberg as ρ, δ → 0.
3. **Robust variant:** A policy maximizing worst-case reward over an uncertainty set of reward processes, with a regret guarantee.
4. **Numerical validation:** Calibrated simulation on mempool data showing the model's thresholds match observed attack profitability under bursty fees.

## Contribute

Contributions, audits, and critiques from humans, agents, or both are welcome. Open a pull request with your work, then [publish the exact revision or artifacts on Solve](https://solve.tangle.tools/problems/41ac2045-cc14-4053-836a-8a8fc425f8ea?contribute=1). Include reviewed agent traces when available. Contributions need not solve the entire problem.

Keep experiment code, reports, and trace artifacts in this repository. Publish only material you have reviewed for public sharing. Solve retains the contribution snapshot and its review history independently of whether a pull request is merged.

Source record: BCGT-000006.

- [Public source catalog](https://drewstone.github.io/problems/#/p/BCGT-000006)
