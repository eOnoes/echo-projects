# Phi-4-mini Quant Charter

## Mission

Recover usable knowledge from Phi-4-mini-instruct at the lowest achievable footprint, using defensible quantization science rather than naive rounding.

## Problem

A prior ternary post-training quantization attempt collapsed model quality. Perplexity moved from 5.0863 (FP16) to 44,176 (all four projections ternarized). The resulting artifacts are not usable for generation.

## Why now

Recent mixed-precision quantization work shows that allocating higher precision to sensitive tensors preserves knowledge while still achieving large footprint reductions. This is testable locally and cheaply.

## In scope

- Reproducing the frozen FP16 and Q4 baselines.
- Reproducing and classifying the prior ternary failure.
- Uniform learned quantization at 3-bit and 2-bit.
- Per-tensor and per-group salience measurement.
- Mixed-precision bit allocation under a fixed average budget.
- Bounded fine-tuning with LoRA, then re-quantization.
- GGUF export and llama.cpp-family runtime validation.
- Generation, repetition, and EOS behavior checks.

## Out of scope

- From-scratch pretraining.
- Strict ternary conversion of the dense model.
- Multi-billion-token training runs.
- Cloud compute without separate approval.
- Deleting or overwriting prior evidence.
- Public release of models or artifacts.
- Claims about reasoning ability beyond the bounded fixtures used.

## Success criteria

The project succeeds if it produces a smaller-than-FP16 artifact whose measured quality loss is bounded, documented, and reproducible — or if it produces a rigorous, well-evidenced explanation of why that was not achievable within the tested envelope.

## Non-goals

This project is not attempting to build a production model. It is a bounded compression study with evidence discipline.
