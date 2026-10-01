# AI Bias Audit

A counterfactual fairness audit of a public sentiment model. It tests whether the model scores otherwise identical sentences differently when only the demographic term changes.

**Built by:** [CyberGemChick](https://github.com/cybergemchick) | AI Red Team

## Problem

A model that treats demographic groups differently can cause discriminatory outcomes at scale. Fairness testing is part of responsible deployment: Article 10 of the EU AI Act requires bias examination of training data for high-risk systems, and the NIST AI RMF (voluntary) lists bias as a risk to manage.

## Threat model

- **Target:** [`cardiffnlp/twitter-roberta-base-sentiment-latest`](https://huggingface.co/cardiffnlp/twitter-roberta-base-sentiment-latest), queried as a black box.
- **Question:** does swapping only the group term change the score?
- **MITRE ATLAS:** AML.T0048 External Harms, AML.T0048.002 Societal Harm.

## Method

| Test | Design | Statistics |
|---|---|---|
| Counterfactual sentiment | 10 templates, each filled with every group in four categories: gender (3), race (5), religion (6), age (3). Articles are chosen by group (for example "An Asian"), so grammar does not add noise. | Group-mean range, one-way ANOVA, Friedman test (the templates are repeated across groups, so observations are paired), eta-squared, Fairlearn selection rates |
| Pronoun by profession | 10 professions with "he", "she" or "they", with matching verbs | Per-profession masculine minus feminine gap |

## Results

**Executed results are not included yet.** The notebook ships without saved outputs. Open `bias_audit.ipynb` in Colab or Jupyter, run all cells, and commit the executed notebook. The audit summary table and charts are generated from the run.

What has been checked without the real model: the notebook was executed end to end against a stand-in scorer to confirm the code paths, the statistics, the Fairlearn metrics, the grammar checks and the charts all run. The stand-in numbers are meaningless and are not reported.

## Security implication

A sentiment score gap shows the model treats groups differently. It is a screening signal for where to look next, not proof of harm. If this model feeds moderation, ranking or triage, the same counterfactual checks belong in its evaluation pipeline.

## Run

```bash
pip install transformers torch fairlearn matplotlib pandas scipy
jupyter notebook bias_audit.ipynb
```

## Limitations

- Ten templates per group is a small sample. Treat flags as a screening signal.
- English only, one model, template sentences.
- Capitalization differs between group terms ("white" vs "Black"), which can itself move scores.
- Sentiment is not a harm measure.
- The pronoun test measures sentiment of the statement, not stereotype association directly.

## References

- [Fairlearn](https://fairlearn.org/)
- [AI Fairness 360](https://aif360.res.ibm.com/)
- [NIST AI Risk Management Framework](https://www.nist.gov/itl/ai-risk-management-framework)
- [Regulation (EU) 2024/1689 (AI Act)](https://eur-lex.europa.eu/eli/reg/2024/1689/oj)
- [MITRE ATLAS](https://atlas.mitre.org/)
