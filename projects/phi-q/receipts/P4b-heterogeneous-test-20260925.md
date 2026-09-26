# Phase 4b Receipt — Decisive Heterogeneous Test (NEGATIVE)

**Project:** phi-q
**Phase:** 4b — Heterogeneous vs uniform at matched size
**Date:** 2026-09-25
**Result:** HETEROGENEOUS ALLOCATION LOSES — thread parked with a measured reason
**GPU used:** local RTX 4070

---

## 1. Question

At **matched size**, does concentrating precision at the sensitive layer edges beat uniform
quantization? This is the falsifiable form of the project's central thesis.

## 2. Design

| Arm | Build | Base |
|---|---|---|
| Control | `Q4_K_S` | uniform pure Q4_K everywhere |
| Treatment | `Q4_K_S` + `--tensor-type-file` overrides | see below |

Treatment overrides, derived from the Phase 3 sensitivity map:

```text
blk\.(0|1|30|31)\.  = q5_k    # highest kurtosis / most sensitive  -> more bits
blk\.(23|24|25|27)\. = q3_k   # lowest sensitivity                  -> fewer bits
```

Edge layers raised, least-sensitive middle layers lowered, chosen so total size matches the
control. Everything else identical: same source checkpoint, same quantizer, same perplexity
binary, same corpus, same parameters.

## 3. Result

| Arm | Size | PPL | vs control |
|---|---|---|---|
| Control — uniform `Q4_K_S` | 2.3456 GB | **5.3568** | — |
| Treatment — heterogeneous | 2.3425 GB | **5.3961** | **+0.73%** |

```text
size delta   -0.0031 GB   (3 MB, effectively matched)
PPL delta    +0.73%
verdict      TREATMENT LOSES
```

For context:

```text
Q4_K_M  (llama.cpp's own mix)   2.4940 GB   5.2206
FP16                            7.6800 GB   5.0553
```

## 4. Why it lost

The reallocation traded 4 layers down into **Q3_K** to pay for 4 layers up into Q5_K. Q3_K sits
past the cliff identified in the Phase 4 curve — the uniform sweep showed Q3_K_M at **+9.24%**
versus Q4_K_M at +3.27%.

So the trade was: a small gain on 4 edge layers, bought with a large loss on 4 middle layers. The
sensitivity differential (~1% spread in weight-space error, 3× in kurtosis) is **not large enough
to pay for that trade.**

This is exactly what the Phase 3 map predicted. Phase 4 bounded the prize at "well under 1%";
Phase 4b now shows that even a carefully targeted allocation **cannot reach that bound** when the
reallocation has to cross the 3-bit cliff to stay size-neutral.

## 5. A result worth noting against us

`Q4_K_M` — llama.cpp's own hand-tuned mix — beats **both** arms: 5.2206 at 2.494 GB. It outperforms
our data-driven allocation *and* pure uniform Q4_K.

llama.cpp's mix wins because it makes finer-grained choices (specific tensors, not whole layers,
and it never drops anything below Q4). An empirical default, tuned by the people who built the
format, beat an allocation derived from our own measurements.

That is worth recording plainly. Our sensitivity map was real and correctly computed; it simply
did not carry enough signal to beat a well-tuned heuristic.

## 6. Decision — PARK

**Heterogeneous quantization is parked for this model, with a measured reason.**

Grounds:

1. The sensitivity spread is flat (projections within 1%, layers within ~2% on weight error).
2. The entire Q4→Q5 quality interval is 1.22 points for 0.321 GB — a small prize.
3. The one allocation we built and measured **lost** at matched size (+0.73%).
4. llama.cpp's existing mix already beats it, for free.

**Scope of the claim:** this refutes *this* allocation. It does not prove every possible
heterogeneous design would fail, and it is not reported as such. But it removes the justification
for spending more project time searching for one.

## 7. Where the value actually is

The Phase 4 curve stands on its own and is the real deliverable:

```text
Q4_K_M   2.494 GB   +3.27%   <- 67.5% smaller than FP16
Q8_0     4.085 GB   -0.19%   <- effectively lossless
```

Reducing 7.68 GB to 2.494 GB for 3.27% perplexity is a strong result. The remaining quality gap is
now a **training** problem, not an allocation problem — which is where QAT comes in.

## 8. Artifacts

```text
staging/phi-q-quants/phi4-mini-Q4_K_S.gguf   2.3456 GB   control
staging/phi-q-quants/phi4-mini-hetero.gguf   2.3425 GB   treatment
staging/phi-q-quants/hetero-tensor-types.txt
staging/hetero-quant2.log   staging/hetero-ppl.log
```

Nothing outside `staging/` modified. The frozen FP16 control and all prior artifacts untouched.

## 9. Bugs hit and fixed during this phase

Both were invocation errors on my side, recorded because they cost time:

1. **Flag/positional order.** `llama-quantize SRC DST TYPE --tensor-type-file F` fails with
   `invalid nthread '--tensor-type-file'`. Flags must precede positionals.
2. **MSYS path to a native binary.** `--tensor-type-file /e/ternary-lab/...` fails with
   `No such file or directory`. Native Windows binaries need `E:/...` paths.
   Same class of error as the earlier `cmd //c` pitfall — recorded as L-015's family.
