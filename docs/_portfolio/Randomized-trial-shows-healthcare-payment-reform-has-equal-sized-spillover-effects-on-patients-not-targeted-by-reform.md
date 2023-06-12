---
output: 
  md_document:
    variant: markdown+backtick_code_blocks
    preserve_yaml: TRUE
knit: (function(inputFile, encoding) {
      out_dir <- "_portfolio";
      rmarkdown::render(inputFile,
                        encoding=encoding,
                        output_dir=file.path(dirname(inputFile), out_dir))})
title: Reading Notes - Randomized trial shows healthcare payment reform has equal-sized spillover effects on patients not targeted by reform
---

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

# My Reading Notes

**Paper: Randomized trial shows healthcare payment reform has
equal-sized spillover effects on patients not targeted by reform**

*Paper By Liran Einav, Amy Finkelstein, Yunan Ji, and Neale Mahoney*

# What is this paper about?

-   Spillovers in health care

-   Medicare changes how they pay for something to one group, that
    affects a different group

    -   Changes for traditional Medicare affect Medicare Advantage
        patients

    -   At similar magnitude

-   Possible reasons:

    -   High fixed cost, low marginal cost technological innovation

        -   New computer system built for traditional Medicare might be
            easy to extend to MA

        -   (this is the reason they think is most likely - since their
            effects are biggest in hospitals where there are a lot of
            directly affected patients, and their survey of hospital
            admin suggests this is how the admin adjusted to the
            changes)

    -   Provider constraints: not knowing insurance type and/or having
        preferences

# What gap is this paper filling?

1.  Policies that ignore spillovers miss a big part of the effect of the
    policy
2.  Any study that looks at the direct effects only may be biased since
    the "control group" might also be experiencing the treatment effect
3.  Designing payment policies in the presence of spillovers is
    different depending on the number of insurance providers.

# What makes this paper interesting?

-   The TM to MA spillover effects are an important piece of the
    healthcare market

-   This shows how to do spillover studies in general and in health care

-   Spillover effects are important for general equilibrium
    understanding

-   The use of the national randomization of the Traditional Medicare
    Spending change - cool natural experiment

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

# What are the results?

Targeting *traditional medicare* patients with a payment reform that
changes payment from fee-for-service to a bundled comprehensive payment
reduces discharges to post acute care facilities for *both* the targeted
traditional medicare patients *and* non-targeted *Medicare Advantage*
patients, who are presumably only seeing any impact because of spillover
effects. The impacts are roughly the same for both groups (about 3
percentage points).

# What assumptions are being made?

-   Randomization worked

    -   "the (admittedly strong) identifying assumption is that,
        conditional on the covariates, there are no hospital-specific
        time trends, and thus any heterogeneity in the change in
        outcomes across hospitals reflects heterogeneous treatment
        effects."

    -   "To probe the sensitivity to this assumption, we estimate an
        alternative specification where we include hospital-specific
        linear time trends as controls. These time trends are identified
        from the hospital-specific outcomes in 2013 and 2014. In this
        specification, the identifying assumption is that, conditional
        on covariates, there are no hospital-specific deviations from
        the time trend"

-   Non-targeted patients did not get anything directly from the program

-   Enough targeted patients to potentially affect non-targeted as well

# Other Ideas

-   Given more granular (at the time level) data, I think they could
    have shown the time trend analysis for both groups.

```{=html}
<!-- -->
```
-   Maybe could use a bordering MSA design to test what happens in
    non-selected MSAs that border selected MSAs

-   Causal forest to see which covariates predict bigger treatment
    effects

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

# What is the Tweet?

Spillover effects in health care are fascinating. These authors looked
at the spillover effects of an MSA-level randomized Medicare program
aimed at reducing spending for *traditional Medicare enrollees* on the
non-targeted *Medicare Advantage enrollees.* **Both** groups reduced
share discharged to postacute care centers by \~3.3pp.

# 
