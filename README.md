# Autoregulation and Cofactor Redundancy Shape REST Gene Expression

A computational modeling project investigating how negative autoregulation and cofactor redundancy shape gene expression dynamics in a simplified REST–cofactor system.

**Course:** C&S BIO M178 — Quantitative Regulatory Biology and Signal Transduction, UCLA  
**Project:** Final Course Project

## Overview

The RE1-silencing transcription factor (REST) is a transcriptional repressor involved in neuronal differentiation and regulation of neuronal genes. REST functions together with corepressors such as mSin3 and CoREST to suppress gene expression.

In this project, we developed a simplified computational model of the REST–cofactor system to investigate how cofactor redundancy, negative autoregulation, transcription/translation dynamics, and feedback thresholds influence gene expression.

The model represents three repressors:

- **R1:** REST
- **R2:** mSin3
- **R3:** CoREST

Gene expression is inhibited when REST is active **and** at least one of the two corepressors is active:

    REST AND (mSin3 OR CoREST)

When repression is lost, mRNA is transcribed and translated into protein. Once protein concentration exceeds a defined threshold, CoREST is activated, creating a negative feedback loop that can restore repression.

## Model

### Repressor Logic

The model uses Hill functions to represent the binding of each repressor to the promoter:

    H1 = R1 / (R1 + Kd1)
    H2 = R2 / (R2 + Kd2)
    H3 = R3 / (R3 + Kd3)

The promoter is active when the REST–cofactor repression condition is not satisfied:

    Gene expression = NOT(H1 AND (H2 OR H3))

This is implemented as:

    H = 1 - [H1 * (1 - (1 - H2)(1 - H3))]

where `H` represents the effective promoter activation signal.

### Dynamical System

The model tracks four molecular species:

- `pr` — inactive promoter
- `pr_a` — active promoter
- `tr` — mRNA transcript
- `p` — translated protein

The system is described by the following ordinary differential equations:

    dpr/dt   = -k_a * H * pr + k_d * pr_a
    dpr_a/dt =  k_a * H * pr - k_d * pr_a
    dtr/dt   =  k_syn * pr_a - k_deg * tr
    dp/dt    =  k_trans * tr - k_pdeg * p

Corresponding reactions are:

    pr → pr_a              with rate k_a * H
    pr_a → pr              with rate k_d
    pr_a → pr_a + tr       with rate k_syn
    tr → ∅                 with rate k_deg
    tr → tr + p            with rate k_trans
    p → ∅                  with rate k_pdeg

CoREST activation is modeled as a threshold-dependent feedback mechanism:

    if p > threshold:
        R3 = active
    else:
        R3 = inactive

This allows the translated protein to indirectly regulate its own production through activation of the repressor complex.

## Results

We used the model to examine several aspects of the regulatory system:

- **Cofactor redundancy:** loss of one corepressor can be partially compensated for by the remaining cofactor.
- **Negative autoregulation:** feedback through CoREST can rescue repression when mSin3 is unavailable.
- **mRNA degradation:** increasing the mRNA degradation rate produces faster oscillatory dynamics.
- **mRNA synthesis:** increasing the synthesis rate changes the amplitude of protein oscillations.
- **Feedback threshold:** changing the protein threshold for CoREST activation alters both gene expression levels and oscillation dynamics.

Together, the simulations demonstrate how relatively simple regulatory logic can produce both stable and oscillatory gene expression behavior.

## Implementation

The project is contained in a single Jupyter notebook:

    REST_Autoregulation.ipynb

The notebook includes the model implementation, numerical simulations, parameter sweeps, and visualization of gene expression dynamics.

## Full Paper

For the complete methodology, results, figures, discussion, and references, see the [**full project paper**](https://drive.google.com/file/d/1SQUSX4f6chKPHbPTgAli4r8VFZF5S5Aa/view?usp=sharing).
