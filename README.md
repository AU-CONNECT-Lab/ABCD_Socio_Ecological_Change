# Analysis code: Socio-Ecological Change Shapes Transdiagnostic Psychopathology in Adolescents Through Default Mode Network Connectivity

Mplus input files for every model reported in the manuscript and Supplementary Information.

## Software
Mplus 8.6. All models use `TYPE = COMPLEX` with `STRATIFICATION = site_id_l_numeric_v51` (study site) and `CLUSTER = rel_family_id_v51` (family).

## Data
Data come from the ABCD Study, Curated Annual Release 5.1 (NIMH Data Archive). Under the NDA Data Use Certification, individual-level data cannot be redistributed, so `data/NMH_analysis_data.csv` and the factor-score files written by step 02 (`mplus/fscores_pfactor_*.dat`) are **not** part of the public release. The `.out` files contain only model-level results. Researchers with ABCD access can rebuild it from the variables listed in `data/codebook.csv`.

- `data/NMH_analysis_data.csv`: comma-delimited, no header row, missing values coded `-9999`. N = 5,838 participants with valid rsFC at baseline and 2-year follow-up (`imgincl_rsfmri_include = 1`). Columns are in the order of the `NAMES` statement in every input file.
- `data/codebook.csv`: column order, variable descriptions, waves, and ABCD source variables.

The rsFC variables (2-year follow-up, Gordon parcellation) are renamed to short names (`DMN_DMN`, `FPN_FPN`, `SN_SN`, `DMN_FPN`, `DMN_SN`, `FPN_SN`) so that they stay distinguishable in Mplus output, which truncates variable names to 8 characters.

## Files and where their results appear

Run the files in numeric order from inside `mplus/`. Steps 03 to 06 read the p-factor scores (`pfac_b0`, `pfac_w3`) produced by step 02. These scores are already stored in the data file, and the CFA output reproduces them exactly.

| File | Model | Reported in |
|---|---|---|
| `01a`–`01e_LGCM_*.inp` | Unconditional latent growth curve models: family conflict, neighborhood safety, family income, material hardship, prosocial school environment | Supplementary Table 1 |
| `02a_CFA_pfactor_b0.inp` | Higher-order CFA of CBCL items (INT, EXT, ATT → P), baseline; saves factor scores | Supplementary Fig. 1, Supplementary Table 7 |
| `02b_CFA_pfactor_w3.inp` | Same model, 3-year follow-up; saves factor scores | Supplementary Fig. 1, Supplementary Table 8 |
| `03_Model1_environment_to_pfactor.inp` | Model 1: socio-ecological intercepts/slopes → latent p-factor (3-year) | Fig. 2a, Supplementary Table 2 |
| `04_Model2_environment_to_rsFC.inp` | Model 2: socio-ecological intercepts/slopes → triple-network rsFC (2-year) | Fig. 2b, Supplementary Table 3 |
| `05_Model3_rsFC_to_pfactor.inp` | Model 3: triple-network rsFC (2-year) → latent p-factor (3-year) | Fig. 2c, Supplementary Table 4 |
| `06a_Mediation1_income_DMN.inp` | Family income → within-DMN rsFC → p-factor | Fig. 3a, Supplementary Tables 5–6 (Model 1) |
| `06b_Mediation2_hardship_DMN.inp` | Material hardship → within-DMN rsFC → p-factor | Fig. 3b, Supplementary Tables 5–6 (Model 2) |
| `06c_Mediation3_hardship_FPN.inp` | Material hardship → within-FPN rsFC → p-factor | Fig. 3d, Supplementary Tables 5–6 (Model 3) |
| `06d_Mediation4_conflict_SN.inp` | Family conflict → within-SN rsFC → p-factor | Fig. 3c, Supplementary Tables 5–6 (Model 4) |
| `06e_Mediation5_hardship_SN.inp` | Material hardship → within-SN rsFC → p-factor | Supplementary Tables 5–6 (Model 5) |
| `06f_Mediation6_conflict_DMN-FPN.inp` | Family conflict → DMN-FPN rsFC → p-factor | Supplementary Tables 5–6 (Model 6) |
| `06g_Mediation7_income_DMN-SN.inp` | Family income → DMN-SN rsFC → p-factor | Supplementary Tables 5–6 (Model 7) |
| `06h_Mediation8_hardship_FPN-SN.inp` | Material hardship → FPN-SN rsFC → p-factor | Supplementary Tables 5–6 (Model 8) |

The `.out` file next to each input is the output that produced the reported values.

### `mplus/precision/` — higher-precision reruns (same models, linear rescaling only)
Mplus prints three decimals, which is too coarse for some reported values. These inputs repeat a model above with one or more variables multiplied by a constant (`DEFINE`); estimates, SEs and CIs are converted back by dividing by that constant. Fit and significance are unchanged (the log-likelihood shifts only by the rescaling constant).

| File | Same model as | Rescaling | Used for |
|---|---|---|---|
| `04_Model2_environment_to_rsFC_x1000.inp` | `04` | all six rsFC × 1,000 | Supplementary Table 3: SEs below 0.0005 (shown as <0.001) and the sign of CI bounds that round to 0.000 |
| `06e_…_pfac_x1000.inp`, `06g_…`, `06h_…` | `06e`, `06g`, `06h` | p-factor × 1,000 (instead of × 10); divide by 100 to return to the table's × 10 metric | Supplementary Table 5: sign of indirect-effect CI bounds that round to 0.000 (Models 5, 7, 8) |

## Model notes
- **Growth models.** Intercept (baseline level) and slope (change) factors are estimated from baseline, 1-year and 2-year assessments, with loadings fixed at 0, 1, 2. Estimator: MLR.
- **P-factor.** 80 CBCL items, treated as ordinal (`CATEGORICAL`), load on internalizing, externalizing and attention factors, which load on a general factor P. Item 34 loads on both INT and EXT. Residual correlations among items are specified in the `WITH` statements. Estimator: WLSMV.
- **Models 1 and 3** use the latent p-factor at 3-year follow-up (WLSMV, `PARAMETERIZATION = THETA`). **Model 2** uses observed rsFC outcomes (MLR). All three adjust for baseline age, sex at birth and baseline p-factor score.
- **Selection of mediation models (a-path rule).** A mediation model is estimated for every socio-ecological domain × rsFC measure pair with a significant domain → rsFC path in Model 2 (`04`; intercept or slope, p < .05), regardless of the rsFC → p-factor association in Model 3. This selects eight pairs (06a–06h), numbered in the order of Supplementary Table 4: by mediator (within-DMN, within-FPN, within-SN, DMN-FPN, DMN-SN, FPN-SN), then by domain (conflict, safety, income, hardship). Both the intercept and slope of the domain are kept in each model.
- **Mediation models** use the p-factor factor scores and share one specification: the rsFC mediator is regressed on the intercept and slope; the 3-year p-factor is regressed on the mediator, the intercept and slope, and baseline age, sex at birth and baseline p-factor. Indirect effects come from `MODEL INDIRECT` with 5,000 bootstrap draws and 95% bias-corrected CIs (`CINTERVAL(BCBOOTSTRAP)`). The p-factor scores and the rsFC mediator are multiplied by 10 (`DEFINE`) in every mediation model. Mediation coefficients are on this rescaled metric. In 06c (Model 3, hardship → within-FPN), 4,998 of the 5,000 bootstrap draws completed; all other models completed 5,000.
- **Analytic N.** Missing data are handled with each estimator's default (FIML for MLR/ML; pairwise for WLSMV). N = 5,838 for most models. Exceptions: the 3-year CFA (N = 5,448, cases with 3-year CBCL data); Model 3 (N = 5,830, because Mplus drops 8 cases missing on the rsFC predictors); and the LGCMs, which drop cases missing on all three waves (e.g., N = 5,721 for family income).
- `06b` keeps a few settings that do not change the model but do affect the bootstrap draws; comments in the file explain them. Leaving them in reproduces the reported CIs exactly.
