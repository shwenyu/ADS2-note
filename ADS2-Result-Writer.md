---
name: ads2-result-writer-discussion
description: Convert ADS2 statistical outputs into marking-guide-aligned R Markdown writing. Use after the user has run R code and provides results, or when the user asks for method justification, hypotheses, assumptions, result reporting, interpretation, discussion, limitations, or next-step wording for ADS2.
---

# ADS2 Skill 3 — Result Writer & Discussion Bank

## 0. Purpose

This Skill is a **writing reference bank** for ADS2 statistical results.

It converts known method choices and user-provided R output into polished R Markdown text:

1. method justification;
2. hypotheses;
3. assumption statements;
4. result reporting;
5. post-hoc interpretation;
6. effect size interpretation;
7. plain-language explanation;
8. biological / practical discussion;
9. limitations;
10. next steps.

Core rule:

    If it is written, it must be evidenced.
    If it is computed, it must be interpreted.
    If a number is not provided, do not invent it.

This Skill complements:

- **Skill 1 — Exam Triage & Method Selection**: decides the method.
- **Skill 2 — Code Reference Bank**: provides R code.
- **Skill 3 — Result Writer & Discussion Bank**: writes the answer text after output is known.

---

## 1. Default behavior

When the user provides output and asks for writing:

1. identify the method branch;
2. extract only numbers actually provided;
3. write the relevant paragraph(s);
4. include statistic, df, p-value, estimate, CI, and effect size where available;
5. add context-based interpretation;
6. include limitations and next steps if requested or expected by the question.

Do not generate code unless the user asks for code.

Do not re-select the method unless the provided method is clearly inconsistent with the question or output.

---

## 2. Required input

Ask for missing values only when they are necessary for accurate writing.

Minimum:

| Need | Examples |
|---|---|
| Question/task | "Does dose affect tooth length?" |
| Method used | two-way ANOVA, chi-square GOF, bootstrap, regression |
| Important R output | test statistic, df, p-value, CI, estimates |
| Variable names | response, predictor(s), groups |
| Units/context | mm, years, proportion satisfied, disease probability |

Method-specific input:

| Method | Useful output |
|---|---|
| t-test | group means, mean difference, t, df, p-value, CI, Cohen's d |
| One-way ANOVA | F, df1, df2, p-value, eta-squared, TukeyHSD |
| Two-way ANOVA | F/p for factor A, factor B, interaction, partial eta-squared, post-hoc |
| Paired design | before mean, after mean, difference, paired t or ANOVA on difference |
| Chi-square GOF | observed counts, expected counts, χ², df, p-value, residuals |
| Chi-square independence | contingency table, χ², df, p-value, expected counts, Cramer's V |
| Bootstrap | observed statistic, bootstrap CI, B, direction of difference |
| Linear regression | slope, 95% CI, p-value, R², diagnostic notes |
| Logistic regression | odds ratio, CI, p-value, event coding |
| Poisson regression | rate ratio, CI, p-value, dispersion |
| Bayesian probability | prior, sensitivity, specificity, posterior, PPV/NPV |
| Clustering | variables, scaling, k, cluster pattern, validation/caution |

If the user provides incomplete output, say briefly:

    I can write the structure now, but to fill the result sentence accurately I still need: [missing values].

---

## 3. Output style

Default language:

- Write final R Markdown paragraphs in **polished English**, because ADS2 reports are usually submitted in English.
- If the user asks in Chinese or asks for explanation, give a concise Chinese explanation before/after the English paragraph.

Style rules:

1. concise paragraphs;
2. no unsupported numbers;
3. no generic theory dump;
4. no "significant" without the statistic and p-value where relevant;
5. no "accept H0";
6. no causal language unless the design supports causality;
7. explain effect size and uncertainty, not only p-value;
8. keep biological/practical interpretation tied to the question.

Preferred wording:

- Use "provides evidence for..." instead of "proves..."
- Use "failed to reject the null hypothesis" or "did not provide clear evidence..." instead of "accepted the null".
- Use "associated with" for observational data, not "caused by".
- Use "detectable difference" rather than "real difference" unless causality/design justifies stronger wording.

---

## 4. Standard answer structure

For a full result-writing request, write in this order:

1. **Method choice**
2. **Hypotheses**
3. **Assumptions**
4. **Main result**
5. **Post-hoc / follow-up result**
6. **Plain-language interpretation**
7. **Discussion**
8. **Limitations**
9. **Next step**

For a short request, return only the requested section.

Example mapping:

| User asks | Return |
|---|---|
| "帮我写 hypothesis" | Section 6 only |
| "帮我解释 ANOVA output" | Method + assumptions + result + interpretation |
| "帮我写 discussion" | Discussion + limitations + next step |
| "帮我把结果写成 Rmd" | Full answer structure |
| "这个 p-value 怎么说" | p-value wording + context interpretation |

---

## 5. Universal writing blocks

### 5.1 Method choice template

Use when explaining why the chosen method fits the question.

    The response variable is [response], which is [continuous/categorical/binary/count]. The explanatory variable(s) are [predictors], which are [variable types]. Therefore, the main task is [comparison of means / association between categorical variables / trend estimation / probability updating / prediction].
    I used [chosen method] because [reason connected to variable type and design]. I did not use [alternative] because [reason].

Example:

    The response variable is tooth length (`len`, mm), which is continuous. The explanatory variables are supplement type (`supp`) and vitamin C dose (`dose`), both categorical grouping variables. Therefore, the task is a factorial comparison of mean tooth length.
    A two-way ANOVA with interaction is appropriate because the experiment has two factors, and the effect of supplement may depend on dose.

---

### 5.2 Assumption summary template

Use after diagnostics have been checked.

    The assumptions were assessed using [diagnostic plots / expected counts / study design / bootstrap design]. The key assumptions are [assumptions]. The diagnostics showed [summary]. Therefore, [method] was considered [appropriate / acceptable with caution / inappropriate, so an alternative was used].

If diagnostics are mixed:

    The diagnostics showed some departure from [assumption], so the result should be interpreted cautiously. However, [reason the analysis is still informative / alternative used].

---

### 5.3 Result sentence template

Universal result sentence:

    [Method] showed [clear/no clear] evidence for [effect/association/difference], [statistic] = [value], [df if relevant], p = [p]. The estimated effect was [effect estimate] with [CI/effect size if available]. This means that [plain-language meaning].

---

### 5.4 Non-significant result wording

Use when p ≥ alpha.

    The result did not provide clear statistical evidence that [effect] differs from [null expectation], [statistic] = [value], p = [p]. This does not prove that there is no effect; rather, the data were not strong enough to detect one under this model and sample.

Shorter version:

    There was insufficient evidence to conclude that [effect] differed from [null expectation].

Avoid:

    The groups are the same.
    The null hypothesis is true.
    There is no effect.

---

### 5.5 Significant result wording

Use when p < alpha.

    The result provided evidence that [effect] differed from [null expectation], [statistic] = [value], p = [p]. The direction and size of the effect suggest that [context-specific interpretation].

Avoid:

    The treatment definitely works.
    The result proves causation.
    The p-value shows the effect is large.

---

### 5.6 Effect size wording

    The p-value indicates evidence against the null hypothesis, while the effect size describes the magnitude of the difference or association. Therefore, the result should be interpreted using both the p-value and the effect size.

For eta-squared:

    Eta-squared / partial eta-squared estimates the proportion of variation in the response associated with the factor after accounting for the model structure.

For Cohen's d:

    Cohen's d expresses the group difference in standard deviation units, making the magnitude of the difference easier to compare across contexts.

For Cramer's V:

    Cramer's V describes the strength of association between categorical variables, independent of the sample size.

---

### 5.7 Confidence interval wording

    The 95% confidence interval gives a range of plausible values for the population effect under repeated sampling. A wider interval indicates greater uncertainty. If the interval excludes the null value, it supports a detectable effect at approximately the 5% level.

Null values:

| Effect type | Null value |
|---|---|
| mean difference | 0 |
| slope | 0 |
| correlation | 0 |
| difference in proportions | 0 |
| odds ratio | 1 |
| rate ratio | 1 |

---

## 6. Hypothesis templates

### 6.1 One-way ANOVA

    H0: Mean [response] is equal across all [group] levels.
    H1: At least one [group] level has a different mean [response].
    The significance level was α = 0.05.

---

### 6.2 Two-way ANOVA with interaction

    For [factor A]:
    H0: Mean [response] is equal across [factor A] groups.
    H1: Mean [response] differs across [factor A] groups.

    For [factor B]:
    H0: Mean [response] is equal across [factor B] groups.
    H1: At least one [factor B] group has a different mean [response].

    For the interaction:
    H0: The effect of [factor A] does not depend on [factor B].
    H1: The effect of [factor A] depends on [factor B].

    The significance level was α = 0.05.

---

### 6.3 t-test

Independent t-test:

    H0: Mean [response] is equal between [group 1] and [group 2].
    H1: Mean [response] differs between [group 1] and [group 2].
    The significance level was α = 0.05.

Paired t-test:

    H0: The mean within-subject difference in [response] is zero.
    H1: The mean within-subject difference in [response] is not zero.
    The significance level was α = 0.05.

---

### 6.4 Chi-square goodness-of-fit

    H0: The observed counts follow the expected distribution.
    H1: The observed counts deviate from the expected distribution.
    The significance level was α = 0.05.

---

### 6.5 Chi-square independence

    H0: [Variable 1] and [Variable 2] are independent.
    H1: [Variable 1] and [Variable 2] are associated.
    The significance level was α = 0.05.

---

### 6.6 Bootstrap proportion comparison

    H0: The proportion of [outcome] is the same between [condition 1] and [condition 2].
    H1: The proportion of [outcome] differs between [condition 1] and [condition 2].
    The significance level was α = 0.05.

Note:

    For bootstrap inference, the conclusion is usually based on whether the bootstrap confidence interval for the difference includes 0.

---

### 6.7 Linear regression

    H0: The slope between [predictor] and [response] is zero.
    H1: The slope differs from zero.
    The significance level was α = 0.05.

---

### 6.8 Logistic regression

    H0: [Predictor] is not associated with the odds of [event].
    H1: [Predictor] is associated with the odds of [event].
    The significance level was α = 0.05.

---

### 6.9 Poisson regression

    H0: [Predictor] is not associated with the rate/count of [event].
    H1: [Predictor] is associated with the rate/count of [event].
    The significance level was α = 0.05.

---

### 6.10 Bayesian probability

    No frequentist null-hypothesis test is needed here. The task is to update the probability of [event] after observing [evidence] using Bayes' theorem.

---

## 7. Assumption writing templates

### 7.1 ANOVA assumptions

Balanced or approximately acceptable:

    The ANOVA assumptions were assessed using residual diagnostic plots and the study design. Observations can be treated as independent based on the experimental design. The residual plots showed approximately constant spread and no severe departure from normality. Group sizes were [balanced / sufficiently similar], so the ANOVA was considered acceptable.

If graphical diagnostics only:

    I used graphical diagnostics rather than combining graphical and formal assumption tests, because formal tests can be overly sensitive or underpowered depending on sample size.

If assumption issue exists:

    The residual diagnostics suggested [non-normality / unequal variance / outliers]. Therefore, the ANOVA result should be interpreted cautiously, and a bootstrap or non-parametric sensitivity analysis would be a useful follow-up.

---

### 7.2 t-test assumptions

    The t-test assumes independent observations within each group, an approximately continuous response, and no severe outliers. Welch's t-test was used, so equal variance between groups was not required.

For paired t-test:

    The paired t-test uses the within-subject differences, so the key assumption is that these differences are approximately normally distributed and independent across subjects.

---

### 7.3 Chi-square assumptions

    The chi-square assumptions were considered. The variables are categorical, the categories are mutually exclusive, and each observation contributes to only one cell. The expected counts were [all at least 5 / mostly at least 5], so the chi-square approximation was acceptable.

If expected counts are small:

    Some expected counts were small, so the chi-square approximation may be unreliable. Fisher's exact test or simulation-based inference would be more appropriate.

If independence fails:

    A chi-square test would not be appropriate because observations are not independent. The counts represent [button presses / repeated responses / overlapping samples], so a bootstrap or design-aware analysis is safer.

---

### 7.4 Bootstrap assumptions

    Bootstrap inference was used because the sampling distribution of the statistic was difficult to justify analytically or because standard test assumptions were questionable. The bootstrap resampled the observed data to approximate uncertainty in [statistic]. The key limitation is that bootstrap reflects the observed sampling process and cannot correct biased or non-representative data collection.

---

### 7.5 Linear regression assumptions

    Regression diagnostics were used to assess linearity, constant residual variance, approximate normality of residuals, and influential observations. The residual plots [did not show severe violations / suggested potential issues with ...], so the model was [acceptable / interpreted cautiously].

---

### 7.6 Logistic regression assumptions

    Logistic regression is appropriate because the response is binary. The model assumes independent observations, correct event coding, and an approximately linear relationship between continuous predictors and the log-odds of the event. The result should be interpreted on the odds-ratio scale.

---

### 7.7 Poisson regression assumptions

    Poisson regression is appropriate for count outcomes when counts are independent and the mean-variance relationship is reasonable. Overdispersion was assessed using the Pearson residual dispersion statistic. If dispersion is much larger than 1, a quasi-Poisson or negative-binomial approach is more appropriate.

---

### 7.8 Clustering assumptions / cautions

    Clustering was treated as exploratory rather than confirmatory. Variables were scaled before clustering so that variables with larger numeric ranges did not dominate the distance calculation. The clusters should not be interpreted as proven biological groups without external validation.

---

## 8. Result reporting templates

### 8.1 One-way ANOVA result

    A one-way ANOVA showed [clear/no clear] evidence that mean [response] differed across [group], F([df1], [df2]) = [F], p = [p], η² = [eta]. This suggests that [plain-language interpretation].
    Because the overall ANOVA [was/was not] significant, Tukey HSD post-hoc comparisons [were used / were not necessary] to identify which groups differed.

---

### 8.2 Two-way ANOVA result

Write interaction first:

    The interaction between [factor A] and [factor B] was [significant/not significant], F([df1], [df2]) = [F], p = [p], partial η² = [eta]. This means that the effect of [factor A] [depends / does not clearly depend] on [factor B].

Then main effects:

    There was [clear/no clear] evidence for a main effect of [factor A], F([df1], [df2]) = [F], p = [p]. There was also [clear/no clear] evidence for a main effect of [factor B], F([df1], [df2]) = [F], p = [p].

If interaction is significant:

    Because the interaction was meaningful, the main effects should be interpreted cautiously. The most informative interpretation comes from comparing [factor A] within each level of [factor B], or vice versa.

---

### 8.3 TukeyHSD post-hoc result

    Tukey HSD post-hoc comparisons showed that [group A] differed from [group B] by [difference] [units], 95% CI [lower, upper], p = [p]. This indicates that [plain-language direction].
    In contrast, [group C] and [group D] did not differ detectably, p = [p].

If many pairwise results:

    The strongest post-hoc difference was between [group A] and [group B], while [other non-significant pairs] did not show clear evidence of a difference.

---

### 8.4 t-test result

    Welch's two-sample t-test showed [clear/no clear] evidence that mean [response] differed between [group 1] and [group 2], t([df]) = [t], p = [p]. The estimated mean difference was [difference] [units], 95% CI [lower, upper]. This suggests that [plain-language interpretation].

Paired:

    The paired t-test showed [clear/no clear] evidence that the mean within-subject change in [response] differed from zero, t([df]) = [t], p = [p]. The mean change was [difference] [units], 95% CI [lower, upper].

---

### 8.5 Chi-square goodness-of-fit result

    A chi-square goodness-of-fit test showed that the observed distribution [deviated/did not clearly deviate] from the expected distribution, χ²([df]) = [value], p = [p]. This means that the data are [unlikely / not clearly inconsistent] with the expected [Mendelian/theoretical] distribution.

Residuals:

    The largest standardized residuals were found in [cell/category]. This indicates that [category] was [underrepresented/overrepresented] relative to expectation and contributed most strongly to the overall deviation.

---

### 8.6 Chi-square independence result

    A chi-square test of independence showed [clear/no clear] evidence of an association between [variable 1] and [variable 2], χ²([df]) = [value], p = [p], Cramer's V = [V]. This means that the distribution of [variable 2] [differs/does not clearly differ] across levels of [variable 1].

Residuals:

    The cells contributing most strongly to the association were [cells], where observed counts were [higher/lower] than expected.

---

### 8.7 Bootstrap proportion result

    The observed proportion of [outcome] was [p1] for [condition 1] and [p2] for [condition 2]. Using [B] bootstrap resamples, the 95% CI was [CI1] for [condition 1] and [CI2] for [condition 2]. The 95% CI for the difference ([condition 2] - [condition 1]) was [CI_diff].
    Because the difference interval [does/does not] include 0, the data [support/do not provide clear support for] a difference in [outcome]. The direction of the effect suggests that [condition] had a higher proportion of [outcome].

---

### 8.8 Linear regression result

    Linear regression showed that [response] changed by [slope] [response units] per one-unit increase in [predictor], 95% CI [lower, upper], p = [p]. The model explained [R²]% of the variation in [response]. This means that [plain-language interpretation].

Prediction:

    The predicted [response] at [new predictor value] was [estimate], with a prediction interval of [interval]. This prediction should be interpreted cautiously if the new value lies outside the observed predictor range, because that would require extrapolation.

---

### 8.9 Correlation result

    The correlation between [x] and [y] was [r], p = [p]. This indicates a [weak/moderate/strong] [positive/negative] association. However, correlation describes association only and does not establish causation.

---

### 8.10 Logistic regression result

    Logistic regression showed that [predictor] was [associated/not clearly associated] with the odds of [event], odds ratio = [OR], 95% CI [lower, upper], p = [p]. This means that [level/increase in predictor] was associated with [higher/lower/no clear change in] odds of [event].

Important:

    If OR > 1, odds increase.
    If OR < 1, odds decrease.
    If CI includes 1, evidence is not clear at α = 0.05.

---

### 8.11 Poisson regression result

    Poisson regression showed that [predictor] was [associated/not clearly associated] with the rate/count of [event], rate ratio = [RR], 95% CI [lower, upper], p = [p]. This means that [level/increase in predictor] was associated with [higher/lower/no clear change in] expected count.

Overdispersion:

    The dispersion statistic was [dispersion]. Because this is [close to / much larger than] 1, the Poisson assumption was [reasonable / questionable], and [standard Poisson / quasi-Poisson] interpretation is preferred.

---

### 8.12 Bayesian diagnostic result

    Using Bayes' theorem, the posterior probability of [event] given [evidence] was [posterior]. This differs from sensitivity because sensitivity is P(test positive | event), whereas the posterior is P(event | test positive).

Repeated testing:

    For a second independent positive test, the posterior after the first test becomes the new prior. After updating again, the posterior probability becomes [posterior2].

---

### 8.13 Clustering result

    The clustering analysis identified [k] groups based on [variables]. Because the variables were scaled before clustering, each variable contributed comparably to the distance calculation. The clusters appear to differ mainly in [features].
    This should be interpreted as exploratory structure rather than confirmed biological classification.

---

## 9. Discussion templates

### 9.1 General discussion

    The main finding is that [result]. Practically / biologically, this suggests that [meaning]. One possible explanation is [mechanism], although [confounder/limitation] could also contribute.
    A useful next step would be [specific experiment or analysis], because it would test [specific uncertainty]. If additional samples are proposed, they should be justified by power analysis rather than by a general desire for a smaller p-value.

---

### 9.2 Limitation paragraph

    A key limitation is [limitation]. This matters because [why it affects interpretation]. Therefore, the result should be interpreted as evidence for [careful claim], not as proof of [overclaim].

Examples of good limitation types:

- non-independent observations;
- small sample size;
- unbalanced groups;
- missing data;
- observational rather than experimental design;
- possible confounding;
- measurement error;
- extrapolation outside observed range;
- sparse expected counts;
- model assumption concerns.

---

### 9.3 Next-step paragraph

    The most informative next step would be [specific follow-up]. This would address [specific limitation/uncertainty] by [mechanism]. If the aim is to improve statistical power, the number of additional samples should be determined using power analysis based on a biologically meaningful effect size.

Avoid:

    More data should be collected.

Better:

    A follow-up experiment should collect [specific data] from [specific groups] to test whether [specific mechanism/uncertainty] explains the observed pattern.

---

### 9.4 Vitamin C / ToothGrowth style discussion

    The results suggest that supplement type and dose both matter for tooth growth, and the effect of supplement may depend on dose. If orange juice performs better at low doses but not at high doses, the new formulation may only be a reasonable substitute when delivered at sufficiently high dose.
    The next step should not simply be increasing sample size. More informative follow-up work would include adding a non-treated or very-low-dose control, testing long-term stability and toxicity, and investigating bioavailability to explain why the formula performs differently across doses.

---

### 9.5 Mendelian mutation / survival style discussion

    The deviation from the expected Mendelian ratio suggests that [genotype] may reduce survival or viability. If the underrepresented group is homozygous mutant, this may indicate embryonic or early postnatal lethality.
    Useful next steps include timed embryo collection to identify the developmental stage of loss, tissue-specific expression analysis to identify where the gene is active, and molecular analysis of surviving mutants to search for compensatory pathways or modifier loci.

---

### 9.6 Coffee shop / button-press style discussion

    The data suggest that recorded satisfaction responses were higher during [condition]. However, the data are button presses rather than independent student-level responses, so the result should be interpreted as evidence about recorded satisfaction responses, not as a perfectly controlled survey of unique students.
    A better follow-up would collect one response per student, record whether the same student attends both schedules, and combine satisfaction with operational outcomes such as sales, staffing cost, and peak demand.

---

### 9.7 Regression / trend discussion

    The regression result suggests that [response] tends to [increase/decrease] as [predictor] increases. The slope quantifies the average rate of change, while the confidence interval describes uncertainty around that estimate.
    This trend should not be extrapolated beyond the observed range of [predictor]. A useful next step would be to collect more data near [important range] or include additional predictors that may explain residual variation.

---

### 9.8 Bayesian diagnostic discussion

    The posterior probability depends strongly on the prior/base rate. Even a highly sensitive test can have a limited positive predictive value when the condition is rare. Therefore, the result should be interpreted as an updated probability after combining the test evidence with the baseline probability.

---

## 10. Anti-pattern bank

### 10.1 P-value overclaim

Do not write:

    The p-value is significant, so the treatment works.

Write:

    The test provides evidence that group means differ. The effect size and post-hoc comparisons indicate which groups differ and how large the difference is.

---

### 10.2 Accepting the null

Do not write:

    We accept the null hypothesis, so there is no difference.

Write:

    The data did not provide clear evidence of a difference. This does not prove the groups are identical.

---

### 10.3 Causal overclaim

Do not write:

    X caused Y.

Write:

    X was associated with Y.

Use causal language only if the study design is randomized/experimental and the question supports it.

---

### 10.4 Generic sample-size next step

Do not write:

    We should increase sample size.

Write:

    If a larger sample is needed, power analysis should determine how many additional observations are required to detect a biologically meaningful effect.

---

### 10.5 Count-data trap

Do not write:

    The chi-square test is appropriate because the data are counts.

Write:

    The chi-square test is appropriate only if observations are independent and cells are mutually exclusive. If these conditions fail, bootstrap or another design-aware method is needed.

---

### 10.6 Trend trap

Do not write:

    The value changed from the first year to the last year, so there is a trend.

Write:

    The trend should be quantified using the regression slope and confidence interval across all time points, not only the endpoint difference.

---

### 10.7 Bayesian conditioning trap

Do not write:

    Sensitivity is the probability that the person has the condition after testing positive.

Write:

    Sensitivity is P(test positive | condition). The posterior probability is P(condition | test positive), which requires Bayes' theorem.

---

## 11. Marking-guide checklist

A strong ADS2 written answer usually includes:

1. response and predictor variables;
2. method justification;
3. hypotheses if using a frequentist test;
4. assumption statement;
5. correct statistical result format;
6. effect size / CI where relevant;
7. post-hoc interpretation where relevant;
8. context-based conclusion;
9. limitation;
10. specific next step.

Quick self-check:

| Question | Must include |
|---|---|
| Did groups differ? | method, F/t, df, p, effect size, post-hoc |
| Did counts match expectation? | observed vs expected, χ², df, p, residuals |
| Were variables associated? | χ² or regression result, effect size, direction |
| Did response change over time? | slope, CI, p, R², no endpoint-only claim |
| Was diagnostic probability updated? | prior, likelihood, posterior, conditioning explanation |
| Was bootstrap used? | statistic, B, CI, whether interval includes null |
| Was clustering used? | variables, scaling, k, exploratory caution |

---

## 12. Request-to-writing mapping

Use this mapping internally.

| User asks for | Return section |
|---|---|
| method justification | 5.1 |
| assumptions | 7 |
| hypothesis | 6 |
| p-value wording | 5.4 / 5.5 / 10.1 |
| effect size explanation | 5.6 |
| confidence interval explanation | 5.7 |
| ANOVA result | 8.1 / 8.2 |
| TukeyHSD explanation | 8.3 |
| t-test result | 8.4 |
| chi-square result | 8.5 / 8.6 |
| bootstrap result | 8.7 |
| regression result | 8.8 |
| correlation result | 8.9 |
| logistic result | 8.10 |
| Poisson result | 8.11 |
| Bayesian result | 8.12 |
| clustering result | 8.13 |
| discussion | 9 |
| limitation | 9.2 |
| next step | 9.3 |
| avoid bad wording | 10 |
| full Rmd writing | 4 + relevant method branch |

---

## 13. Final self-audit before output

Before writing the user's answer, check:

1. Did I use only numbers provided by the user or visible in the output?
2. Did I report statistic, df, p-value, estimate, CI, and effect size where relevant?
3. Did I explain what each result means in plain language?
4. Did I avoid unsupported claims?
5. Did I discuss mechanism, limitation, and next step only when supported?
6. Did I avoid "increase sample size" unless power analysis supports it?
7. Did I preserve the user's chosen method and variable names?
8. Did I warn if the provided method and results appear inconsistent with the question?
9. Did I avoid causal language for observational data?
10. Did I keep the writing concise enough for an ADS2 R Markdown answer?
