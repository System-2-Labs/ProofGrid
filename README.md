<p align="center">
  <img src="assets/pg_icon.png" alt="ProofGrid icon" width="250">
</p>
<p align="center">
  A reasoning benchmark for language models
</p>

ProofGrid is a reasoning benchmark for AI models centered on **proof-based evaluation** rather than final-answer scoring alone.

This repository accompanies the technical report: 

> [**Stress-Testing the Reasoning Competence of LLMs With Proofs Under Minimal Formalism**](paper/proofgrid.pdf)

which is an extended version of the 2025 EMNLP Findings paper:

> [**Stress-Testing the Reasoning Competence of Language Models With Formal Proofs** ](https://aclanthology.org/2025.findings-emnlp.661/)
> EMNLP Findings 2025

The extended technical report has a lot of new material (new tasks, results, concepts, methods, and analyses) that did not appear in the EMNLP paper.

ProofGrid is designed to evaluate not just whether a model arrives at a correct answer, but whether it can **produce, analyze, verify, and manipulate rigorous reasoning artifacts**. Its central premise is that final-answer accuracy alone is an incomplete measure of reasoning competence, especially in mathematics and logic, where correctness depends on the validity of the underlying argument.

## Overview

ProofGrid is a benchmark suite for evaluating reasoning through **machine-checkable proofs** and closely related proof-centered tasks. It includes 15 tasks spanning several proof-based reasoning competencies, including:

- **proof writing**
- **proof checking**
- **proof masking**
- **proof gap-filling**
- **equational reasoning tasks**

A major design goal of the benchmark is to make reasoning judgments:

- **mechanical**
- **reproducible**
- **auditable**
- **fine-grained**

rather than dependent on informal human or LLM judgment, or on answer matching alone.

The benchmark is intentionally built around **minimal formalism**, most notably **NDL**, a tiny natural-deduction language. NDL is exceptionally simple, fully specifiable in a short prompt, and suitable for precise proof checking without the heavy machinery of large theorem-proving ecosystems.

## What makes ProofGrid different?

Many reasoning benchmarks evaluate only the final output. That is convenient, but it can mask something something important: a model can sometimes produce the right answer for the wrong reasons.

ProofGrid instead focuses on the structure and validity of the reasoning itself. It is meant to probe whether models can:

- construct valid proofs,
- check purported proofs for correctness,
- recover missing proof structure,
- reason about proofs as objects,
- and maintain coherence across related reasoning contexts.

The benchmark is also designed to avoid several common confounders:

- reliance on memorized domain knowledge,
- tasks that can be trivially outsourced to standard external solvers,
- excessive dependence on very long context windows,
- and artificial “gotcha” difficulty based on obscure rules or adversarial formatting.

In that sense, ProofGrid is intended not merely as a leaderboard, but as a diagnostic framework for machine reasoning.

## Repository contents

This repository is being released in stages.

### Currently included

#### `prompts/`

This directory contains the prompts for **all benchmark tasks**.

These files define the task-facing interfaces used to evaluate models across the ProofGrid suite.

#### `model_results/`

This directory contains the detailed **YAML result archives** for **all tasks**.

These YAML files are intentionally comprehensive and therefore quite **large**. They include all individual model-result records associated with the benchmark tasks and are meant to support much deeper analysis than headline aggregate scores alone.

### Forthcoming

#### `src/`

The evaluation code will be released in the near future under the `src` directory.

This will include the benchmark’s evaluation machinery and supporting code needed to reproduce scoring and related analyses more directly from the released materials.

## Benchmark philosophy

ProofGrid is built around a specific view of reasoning evaluation.

In particular, the benchmark aims to measure reasoning in settings where models cannot easily succeed by retrieving memorized content or by exploiting shortcuts that bypass the intended inferential work. To support that goal, the tasks are abstract, symbolic, and tightly specified.

The benchmark also emphasizes discriminative power. It is designed not only to identify whether top models can solve easy tasks, but to meaningfully separate weak, medium, and strong models across a range of difficulty levels.

This includes both:

- foundational tasks that test minimal proof-backed reasoning competence;
- intermediate tasks that remain challenging but tractable;
- and frontier tasks that still leave substantial room for progress.

As models continue to advance, we plan to update these tasks accordingly. 
## NDL and Instrumented Proof Checking 

A large part of ProofGrid uses **NDL**, a minimalist language for writing natural-deduction proofs.

NDL is intended to be:

- expressive enough to represent standard natural-deduction arguments,
- simple enough to specify and interpret precisely,
- and strict enough to expose reasoning failures clearly.

It supports, among other things:

- premise assertions;
- named intermediate results;
- explicit assumption and lemma scopes;
- inference-rule applications;
- and compositional proof structure.

The point of using such a language is not to burden models with arbitrary syntax, but to make proof correctness computationally analyzable and to enable much finer-grained error analysis than is possible with unconstrained natural-language reasoning traces.

A key part of the benchmark is its instrumented proof checker for NDL. In addition to strict checking, ProofGrid uses an evaluation pipeline that can repair or overlook a wide range of minor surface-level failures while still preserving the proof's underlying logical structure. In practice, models often produce proofs whose global strategy is sound but whose execution contains small syntactic or procedural slips: missing braces or semicolons, bracket mismatches, naming errors around `BY` and `:=`, confusion between left/right projections of conjunctions or disjunctions, harmless double-negation mismatches, and similar low-level issues. The instrumented checker attempts to recover from these in a principled way and then continues evaluation step by step.

This matters because naive strict proof checking can dramatically understate a model's actual ability to discover proofs. The instrumented checker is designed to separate **high-level reasoning competence** from **brittle formal execution**, and to identify the first genuinely substantive failure rather than collapsing every imperfection into a single hard reject. At the same time, it does **not** excuse major logical mistakes: if a purported conclusion does not follow from the current assumption base, or if a rule is applied in a fundamentally unsound way, the proof still fails. This makes the checker both more forgiving about cosmetic execution noise and more informative as a diagnostic tool.

## Current status

This repository is under active preparation and expansion.

At present, it includes:

- the full prompt set for all tasks in `prompts/`, and
- the detailed YAML model-result files for all tasks in `model_results/`.

The evaluation code under `src/` will be added shortly.

## Citation

If you use ProofGrid in academic work, please cite the accompanying paper.

## License

- **Code:** MIT License
- **Dataset / benchmark data:** CC BY 4.0

