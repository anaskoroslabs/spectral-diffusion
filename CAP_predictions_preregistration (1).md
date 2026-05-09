# Pre-registered predictions: CAP Sleep Database

**Author:** Chris M. Roy (Anaskoros Labs)
**Committed:** 2026-05-09T17:24:16Z (UTC)
**Dataset:** CAP Sleep Database, PhysioNet, v1.0.0 (DOI 10.13026/C2VC79)
**Cohort:** 108 polysomnographic recordings (16 healthy controls, 92 pathological: 40 NFLE, 22 RBD, 10 PLM, 9 insomnia, 5 narcolepsy, 4 SDB, 2 bruxism)
**Status:** committed prior to running any |K|, eff_rank, or K/eff_rank analysis on this dataset. No CAP file has been opened, parsed, or inspected at the time of this registration.

## Pipeline (frozen)

Identical to the EEG_K_NatComms_revised pipeline:

- 4 s Hann window, 2 s stride, 100 frequency bins (0–50 Hz, 0.5 Hz resolution).
- 30-step sliding window, 2-step stride for |K| / eff_rank / dwell / ratio.
- Step matrix: row differences of 30 × 100 chunk → 29 × 100.
- |K| = |2σC / (1 + C²)|, C = μ/σ, μ = mean(step norms), σ = std(step norms). No free parameters.
- eff_rank = exp(H) where H is Shannon entropy of normalised SVs of the step matrix.
- Stage labels read from .txt REMlogic files; mapping: W = 1, S2/S3/S4 = NREM (0), R = REM (2), S1 = excluded transitional, MT = excluded.
- Channel preference: F3/F4 (frontal) > C3/C4 (central) > O1/O2 (occipital). Reference A1/A2.
- Interval-lookup annotation alignment (no searchsorted).
- All comparisons within-subject paired (t-test + Wilcoxon).
- No retraining, no parameter tuning, no modification.

## Primary predictions (healthy controls, n = 16)

**P1. Wake/NREM |K| ratio replicates Sleep-EDF Telemetry result.**
Predicted: ratio 3.5–5×, 16/16 subjects correct direction.
Threshold for "framework validated": ratio ≥ 3.0× in controls.
Threshold for failure: any control subject with reversed direction, or group ratio < 3×.

**P2. REM clusters with NREM in controls.**
Predicted: K_REM / K_NREM ∈ [1.0, 1.2].
Threshold for failure: K_REM > 1.5 × K_NREM (i.e. REM looking Wake-like) — this would invalidate the "tracks phenomenal consciousness" claim and indicate the Sleep-EDF Telemetry REM result was a montage artefact.

**P3. CAP A-phase |K| elevation within NREM (novel; not in the current paper).**
Predicted: |K|(A) / |K|(B) ∈ [1.5, 2.5] averaged over controls.
Sub-prediction: A3 > A2 > A1, with A1 possibly only 1.2× and A3 ≥ 2×.
Threshold for confirmation: A/B ratio ≥ 1.5 in healthy controls, with at least 12/16 subjects showing A > B direction.
Threshold for failure: A/B < 1.2 or non-monotonic A1/A2/A3 ordering.

**P4. eff_rank within-NREM inversion (counter-intuitive).**
Predicted: eff_rank(A) < eff_rank(B). A-phases should resemble Wake's compact dimensional regime; B-phases the slow-wave-expanded NREM baseline.
Threshold for confirmation: eff_rank(A)/eff_rank(B) < 0.97, paired t-test p < 0.01.
Threshold for failure: A and B do not dissociate on eff_rank (p > 0.05) — would indicate slow-wave eff_rank story is incomplete.

**P5. CAP rate predicts NREM mean |K| across all 108 subjects.**
Predicted: Spearman ρ(CAP_rate, mean_NREM_K) > 0.4, p < 0.001.
Threshold for failure: ρ < 0.25.

## Pathology-specific predictions

**P6. Insomnia (n = 9):**
NREM K elevated 1.3–1.8× vs controls; Wake/NREM ratio degraded to 2–3× (still discriminative but compressed). Driven by elevated CAP rate documented in this population.

**P7. NFLE (n = 40):**
High within-subject NREM K variance from interictal/ictal bursts (analogous to propofol burst-suppression). Per-subject paired Wake/NREM still discriminates in ≥ 90% of subjects; < 10% with reversed direction due to seizure-dominated NREM windows. A fixed-threshold cross-subject classifier will degrade more than per-subject paired tests.

**P8. RBD (n = 22) — strong, easily falsifiable:**
REM K remains NREM-like: K_REM / K_NREM ∈ [1.0, 1.3]. Atonia loss is muscular, not cortical — EEG spectral geometry should be preserved.
**Falsification:** if RBD REM K_REM / K_NREM > 1.5 (i.e. wake-like), the framework is tracking something closer to cortical-arousal/motor coupling than phenomenal consciousness, and the REM dissociation in healthy sleep needs reinterpretation.

**P9. PLM (n = 10):**
NREM K elevated 1.2–1.5× from limb-movement-associated arousals.

**P10. Narcolepsy (n = 5):**
SOREM transitions create stage-label disagreement with underlying state. Metrics should track underlying state, not label. Predict: a subset of "Wake" labelled segments will show NREM-like K (sleep intrusions). No specific effect-size prediction due to N = 5.

**P11. SDB (n = 4):**
NREM K elevated from respiratory-event-associated arousals. Sample too small for tight effect-size prediction.

**P12. Bruxism (n = 2):**
Unpredictable. EMG contamination of EEG likely. Likely excluded from primary analysis.

## Channel-level prediction

**P13. Channel ranking, healthy controls:**
F3/F4 cleanest, C3/C4 intermediate, O1/O2 noisiest. All preserve Wake > NREM direction. Occipital W/NREM ratio compresses to ~2.5× because slow-wave signature is weakest there.

## Expected exclusions

5–10% of subjects fall out of clean primary analysis due to:
- Lights-on/off mid-recording → insufficient stable Wake baseline;
- Bruxism EMG contamination;
- NFLE subjects with seizure-dense recordings → ambiguous post-arousal windows.

## Decision rules

| Outcome | Interpretation |
|---|---|
| P1 + P2 confirm in controls | Cross-cohort replication achieved (different montage, scorer, software, reference). |
| P1 confirms but P2 fails | Sleep-EDF Telemetry REM result was likely a montage artefact. |
| P1 fails | |K| does not generalise to A1/A2-referenced clinical PSG; framework requires montage-aware revision. |
| P3 confirms | First within-NREM microstructure result; |K| is a candidate CAP-A detector without phase-specific training. |
| P3 fails but P1 confirms | Framework discriminates macrostates only; CAP microstructure requires different geometric primitive. |
| P4 confirms | Mechanistic story for eff_rank inversion (slow-wave drives expansion, A-phase arousals contract) is supported. |
| P4 fails | Dimensional story for the W/NREM eff_rank inversion is incomplete. |
| P5 confirms | |K| as candidate quantitative CAP-rate proxy; opens clinical translation path. |
| P8 falsifies (RBD REM looks wake-like) | Framework tracks cortical-motor coupling rather than phenomenal consciousness; reinterpret REM dissociation. |
| P8 confirms | RBD provides additional dissociation: motor atonia loss without geometric arousal — strong evidence the metrics are not arousal-coupled. |

## Analysis order

1. Healthy controls only — primary tests P1, P2, P3, P4.
2. Cross-subject correlation — P5.
3. Pathology stratification — P6–P12 in order of N (NFLE, RBD, PLM, insomnia, narcolepsy, SDB, bruxism).
4. Channel-comparison subanalysis — P13.

Pathology tests are not gated by control results; if controls fail P1, the pathology results are still informative but interpreted under a broader "framework breaks at A1/A2 reference" hypothesis.

## What this pre-registration commits to

- Effect-size thresholds, not just direction.
- One falsification test (P8) where a specific positive RBD result kills a load-bearing claim.
- One novel positive prediction (P3) the existing paper does not make.
- Pre-stated decision rules for each combination of outcomes.
- No re-running of the pipeline with parameter adjustments after seeing the results. If the pipeline produces an unexpected result, that is the result.

## Author note

The "vibe coding" workflow (specify intent, validate against mental models) is appropriate for development but not for confirmation. This pre-registration exists to force the analysis to confirm or falsify these specific quantitative claims rather than be tuned to fit whatever the data show. RDNF discipline applies: the analysis is designed to kill these predictions.
