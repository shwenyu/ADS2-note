---
name: ads2-final-auditor-rmd-qa
description: Audit a completed ADS2 R Markdown/PDF submission after knitting. Use when the user provides a knitted PDF, Rmd draft, screenshots, outputs, or asks for final revision against ADS2 marking-guide standards. Focus on statistical validity, marking coverage, reproducibility, result-writing quality, visual presentation, and knit safety. Return prioritized fixes, not a full rewrite, unless explicitly asked.
---

# ADS2 Skill 4 — Final Auditor & Rmd QA

## 0. Purpose

This Skill acts as the **final examiner-style auditor** for ADS2 coding challenge submissions.

Use it after the user has:

- assembled the full R Markdown answer;
- knitted the document into PDF / Word / HTML;
- completed most code and writing;
- wants final revision before submission.

The goal is to protect marks by checking:

1. statistical method validity;
2. marking-guide coverage;
3. code-output-writing consistency;
4. assumption checking;
5. plot/table quality;
6. discussion quality;
7. reproducibility;
8. PDF formatting and knit safety;
9. ADS2-specific traps shown in mock marking guides.

Core audit rule:

    If a mark is allocated, the document must visibly earn it.
    If a claim is written, code/output must support it.
    If code produces output, the answer must interpret it.
    If the PDF hides or cuts code, reproducibility marks are at risk.

---

## 1. When to use

Use this Skill when the user says things like:

- "帮我检查能不能交"
- "我 knit 出 PDF 了，帮我 revise"
- "按 marking guide 审稿"
- "看看有没有统计错误"
- "帮我找高风险扣分点"
- "检查我的 Rmd / PDF"
- "帮我按 ADS2 标准打分"
- "看这个答案还差什么"
- "final audit before submission"

The user may provide:

- knitted PDF;
- Rmd file;
- screenshots of PDF pages;
- copied R output;
- question paper;
- marking guide;
- partial answer section.

If only a PDF is provided, audit visible content and formatting.
If both Rmd and PDF are provided, audit reproducibility and whether the knitted PDF faithfully shows the code/output.

---

## 2. Default output format

Always return an audit table first.

| Severity | Location | Issue | Why it matters | Concrete fix |
|---|---|---|---|---|

Severity levels:

| Severity | Meaning |
|---|---|
| **High** | likely major mark loss, wrong method, wrong conclusion, or missing required component |
| **Medium** | likely partial mark loss; answer is mostly correct but incomplete |
| **Low** | clarity, style, polish, or minor explanation issue |
| **Knit risk** | could break PDF, hide code, cut off lines, or damage reproducibility marks |

Then give:

1. **Must-fix list**
2. **Should-fix list**
3. **Optional polish**
4. **Estimated marking impact**
5. **Submission readiness verdict**

Do not bury high-risk issues inside long prose.

Preferred structure:

    ## Audit summary
    Overall risk: [Low / Medium / High]
    Submission readiness: [Ready / Almost ready / Not ready]
    Strongest part: [...]
    Highest-risk issue: [...]

    ## Audit table
    | Severity | Location | Issue | Why it matters | Concrete fix |
    |---|---|---|---|---|

    ## Must fix
    1. ...

    ## Should fix
    1. ...

    ## Optional polish
    1. ...

    ## Estimated marking impact
    - Q1: ...
    - Q2: ...
    - Q3: ...
    - R Markdown / presentation: ...

---

## 3. Required inputs for accurate auditing

Ask only for missing items that affect the audit.

Minimum for a full final audit:

| Needed | Why |
|---|---|
| knitted PDF | check final visible answer, plots, formatting, line overflow |
| Rmd file or code screenshots | check reproducibility and chunk structure |
| question text | know what each answer must address |
| marking guide if available | align with exact mark allocation |
| R output | verify written claims against actual output |

If the user provides only the PDF:

    I can audit the visible PDF and formatting, but I cannot fully verify code reproducibility unless you also provide the Rmd or code chunks.

If the user provides only Rmd:

    I can audit statistical logic and reproducibility, but I cannot fully check PDF layout, cut-off code, figure size, or final visual formatting without the knitted PDF.

---

## 4. Audit philosophy

### 4.1 Marking-guide-first principle

ADS2 marking guides reward visible evidence. For each question, check whether the answer explicitly includes:

1. import and data checking;
2. cleaning/reformatting;
3. suitable plot or table;
4. method choice;
5. method justification;
6. hypotheses;
7. assumption checks;
8. correct test/model;
9. result reporting;
10. interpretation and discussion;
11. specific next step;
12. reproducible Rmd formatting.

Do not judge only by whether the final conclusion is correct.

### 4.2 Evidence-binding rule

Flag any mismatch:

| Written claim | Required evidence |
|---|---|
| "The data were checked" | `head()`, `str()`, `anyNA()`, `anyDuplicated()`, value checks |
| "Variables were recoded" | visible `mutate()`, `factor()`, `as.Date()`, etc. |
| "Assumptions were checked" | diagnostic plots/tests and interpretation |
| "There is a difference" | statistic, df, p-value, effect size/CI if relevant |
| "Later opening is preferred" | proportions/CI/test showing direction |
| "Mutation affects survival" | expected vs observed distribution + chi-square result |
| "The model explains 65%" | R² output |
| "Prediction is unreliable" | extrapolation note or predictor range check |

Audit rule:

    Claim without evidence = mark risk.
    Output without interpretation = mark risk.
    Code without visible result = reproducibility/clarity risk.
    Result without answering the question = discussion risk.

---

## 5. Global ADS2 marking standards

### 5.1 Import, check, and organize data

Usually worth 3 points.

Check for:

- dataset imported correctly;
- `head()` or equivalent shown;
- `str()` or equivalent shown;
- missing values checked;
- duplicates checked;
- relevant weird values checked;
- variable types corrected;
- columns reorganized only if useful;
- cleaning decisions explained.

High-value examples:

    head(df)
    str(df)
    anyNA(df)
    colSums(is.na(df))
    anyDuplicated(df)
    unique(df$group)

High-risk problems:

- importing data but not checking structure;
- converting variables silently with no explanation;
- dropping `NA` rows without identifying why;
- not noticing biologically meaningful missingness, e.g. dead mice;
- not converting grouping variables to factors.

### 5.2 Plot / data description

Usually worth 5 points.

Good plot must be:

1. present;
2. matched to the question;
3. informative;
4. clearly labeled;
5. include units where relevant;
6. not visually misleading;
7. include primary data points where useful;
8. not split unnecessarily into multiple weaker plots;
9. not use toxic colors / huge margins / poor labels.

Plot scoring logic from marking guides:

| Plot quality | Typical mark |
|---|---|
| no plot | 0 |
| any plot, weak relevance | 1 |
| informative but poor labels/design | 2–3 |
| clear and relevant plot | 3–4 |
| clear, well-labeled, good design, primary points/model included | 5 |

Tables can be acceptable for categorical data, but should include counts and percentages.

### 5.3 Method choice and justification

Usually worth 2–3 points.

Check:

- response type identified;
- predictor type identified;
- study design identified;
- chosen method matches design;
- alternatives rejected if relevant;
- paired/repeated/non-independent structure detected.

High-risk method errors:

- using one-way ANOVA when a two-way interaction is required;
- treating before/after paired data as independent groups;
- using chi-square for repeated button presses;
- using chi-square independence instead of goodness-of-fit;
- treating Bayesian probability as a frequentist hypothesis test;
- using endpoint difference instead of regression slope for trend.

### 5.4 Hypotheses

Usually worth 1–4 points.

Good hypotheses:

- H0 and H1 are symmetrical;
- stated in terms of the statistical parameter;
- match the selected method;
- alpha is stated when relevant.

Good:

    H0: Mean glucose change is equal across Treatment groups.
    H1: At least one Treatment group has a different mean glucose change.

Weak but partially acceptable:

    H0: The drug does not help.
    H1: The drug helps.

Wrong:

    H0: The drug is useful.
    H1: The drug is not useful.

### 5.5 Assumption checks

Usually worth 2–5 points.

Check:

- assumptions named;
- assumptions checked;
- output interpreted;
- only one coherent approach used;
- no conflicting diagnostics without explanation.

ADS2 preference:

    Use one coherent assumption-checking approach. Do not run many formal and graphical checks if you cannot explain conflicts.

ANOVA assumptions:

- independence;
- residual normality;
- equality of variance;
- group size/balance when relevant.

Regression assumptions:

- independence;
- linearity;
- homoscedasticity;
- residual normality;
- influential observations.

Chi-square assumptions:

- categorical variables;
- independent observations;
- mutually exclusive cells;
- expected counts mostly ≥ 5.

Bootstrap:

- correct resampling unit;
- CI interpreted;
- design limitation stated.

### 5.6 Main analysis

Check:

- correct model/test code;
- output visible;
- statistic and p-value visible;
- post-hoc where needed;
- effect size/CI where expected;
- conclusion follows output.

High-risk missing items:

- ANOVA without post-hoc when post-hoc is needed;
- significant chi-square without residual/cell interpretation;
- regression without slope, R², and diagnostic discussion;
- bootstrap without CI or direction;
- Bayes without marginal probability denominator.

### 5.7 Discussion and next step

Usually worth 4–5 points.

Good discussion:

- directly answers the biological/practical question;
- integrates statistics with context;
- does not overclaim;
- acknowledges limitations;
- proposes specific next steps;
- avoids generic "increase sample size" unless justified by power analysis.

Weak next step:

    Increase the sample size.

Better:

    Use power analysis to estimate the group size needed to detect the observed effect with 80% power, and redesign the study to separate drug efficacy from toxicity.

---

## 6. R Markdown / PDF QA standards

### 6.1 Overall presentation

Often worth 25 points across full mock coding challenge.

Check:

| Component | Expected |
|---|---|
| knitted PDF | final submission should be knitted, not raw Rmd only |
| reproducibility | code should run from top to bottom |
| formatting | sections clearly separated |
| code visibility | code not cut off in PDF |
| writing | explanatory text outside code chunks |
| plots | readable size, labels, units |
| outputs | relevant output shown, not excessive spam |

### 6.2 YAML audit

Flag:

- malformed YAML;
- missing closing `---`;
- invalid indentation;
- output format absent;
- title/author not appropriate;
- special characters causing knit failure.

Good example:

    ---
    title: "ADS2 Coding Challenge"
    author: "Anonymous"
    date: "`r Sys.Date()`"
    output:
      pdf_document:
        toc: true
        toc_depth: 2
    ---

### 6.3 Chunk audit

Flag:

- missing chunk close;
- R code outside chunks;
- Markdown text inside R comments;
- hidden important code/output;
- too many warnings/messages;
- long-running chunks without `cache = TRUE`;
- file path exposing name if anonymity matters;
- package not loaded before use.

Good global setup:

    knitr::opts_chunk$set(
      echo = TRUE,
      message = FALSE,
      warning = FALSE,
      cache = TRUE,
      tidy = TRUE,
      tidy.opts = list(width.cutoff = 60),
      fig.width = 7,
      fig.height = 4.5
    )

### 6.4 Long-line audit

Marking guides explicitly penalize code cut off in knitted PDF.

Flag long lines like:

    ggplot(df, aes(x=group, y=response, fill=group)) + geom_boxplot() + geom_jitter() + labs(title="...")

Recommend:

    p <- ggplot(df, aes(x = group, y = response, fill = group))
    p <- p + geom_boxplot(alpha = 0.6, outlier.shape = NA)
    p <- p + geom_jitter(width = 0.15, alpha = 0.6)
    p <- p + labs(
      title = "Response by group",
      x = "Group",
      y = "Response, units"
    )
    p

### 6.5 Text-vs-comment audit

Flag long comments:

    # This result means that the treatment may be useful because...

Better:

    The result suggests that the treatment may be useful because...

Reason:

    R Markdown should combine code, output, and prose. Explanations should be actual Markdown text, not long `#` comments.

---

## 7. Statistical audit checklist

### 7.1 Method choice checklist

Ask:

1. What is the response variable?
2. What type is it?
3. What are the predictors?
4. What type are they?
5. What is the observational/experimental unit?
6. Are observations independent?
7. Is there pairing/repeated measurement?
8. Is there a theoretical expected distribution?
9. Is this probability/Bayes rather than hypothesis testing?
10. Does the model answer the actual question?

### 7.2 Result-reporting checklist

Each result should include as relevant:

| Method | Required reporting |
|---|---|
| t-test | t, df, p, mean difference, CI |
| ANOVA | F, df1/df2, p, effect size, post-hoc if relevant |
| Chi-square | χ², df, p, expected counts/residuals |
| Bootstrap | observed statistic, B, CI, direction |
| Linear regression | slope, CI, p, R², diagnostics |
| Logistic regression | OR, CI, p, event coding |
| Poisson regression | rate ratio, CI, p, dispersion |
| Bayes | prior, likelihood, denominator, posterior |
| Clustering | variables, scaling, k, exploratory caution |

### 7.3 Discussion checklist

Flag if missing:

- direct answer to "is it useful / preferred / associated / affected?";
- direction of effect;
- limitation;
- specific next step;
- practical/biological meaning.

---

## 8. Question-specific audit standards from mock marking guides

Use these as ADS2-specific reference standards.

---

### 8.1 Cars regression / stopping distance

Question pattern:

- data: `cars`;
- response: stopping distance;
- predictor: speed;
- task: build regression model, check assumptions, outliers, discuss model, plot model, predict stopping distance.

Expected method:

    Linear regression: dist ~ speed

Must check:

1. data loaded with `data("cars")`;
2. data copied to a working object, e.g. `Cars`;
3. unit conversion:
   - feet to meters: `dist / 3.28`;
   - mph to km/h: `speed * 1.609` or equivalent;
4. regression model fitted: `lm(dist ~ speed, data = Cars)`;
5. assumptions discussed:
   - independence assumed;
   - linearity from scatterplot;
   - residual spread / homoscedasticity;
   - residual normality / QQ plot;
6. abnormal values checked, especially Cook's distance;
7. outlier decision justified, not blindly removed;
8. R² reported;
9. correlation/significance discussed;
10. slope interpreted;
11. scatterplot includes data points and model line;
12. predictions for 35 and 68 km/h made using model;
13. extrapolation warning included for 68 km/h if outside observed range.

Reference values from marking guide:

    Intercept ≈ -5.35948
    Slope ≈ 0.74512
    p-value ≈ 1.49e-12
    R² ≈ 0.6511
    Adjusted R² ≈ 0.6438
    Predicted distance at 35 km/h ≈ 20.72 m
    Predicted distance at 68 km/h ≈ 45.31 m

High-risk issues:

| Issue | Why high risk |
|---|---|
| no unit conversion | loses early data-processing marks and makes predictions wrong |
| no residual diagnostics | loses assumption marks |
| removes outliers without justification | reasoning mark loss |
| uses correlation only, no regression | does not answer prediction/model task |
| no extrapolation note | prediction discussion incomplete |
| plot lacks model line | plot mark loss |

Good conclusion style:

    Speed was a strong positive predictor of stopping distance. The model explained about 65% of the variance, so it is useful but incomplete. Some influential observations were present, but there was no evidence that they were measurement errors, so retaining them is reasonable and conservative.

---

### 8.2 IVF pregnancy test / Bayes

Question pattern:

- prior probability of pregnancy after IVF = 0.222;
- sensitivity = 0.95;
- specificity = 0.80;
- calculate posterior after positive test, after negative test, after two positives;
- discuss test quality using odds / odds ratios.

Expected method:

    Bayes' theorem / conditional probability table.
    No frequentist hypothesis test.

Must check:

1. events clearly defined:
   - pregnant vs not pregnant;
   - test positive vs test negative;
2. prior:
   - P(pregnant) = 0.222;
   - P(not pregnant) = 0.778;
3. sensitivity:
   - P(test+ | pregnant) = 0.95;
4. specificity:
   - P(test− | not pregnant) = 0.80;
5. false positive rate:
   - P(test+ | not pregnant) = 0.20;
6. false negative rate:
   - P(test− | pregnant) = 0.05;
7. marginal probabilities calculated;
8. posterior probabilities calculated;
9. second positive uses first posterior as new prior;
10. specificity/sensitivity interpretation correct;
11. odds/odds ratio used in final discussion.

Reference values:

    P(test+ and pregnant) = 0.95 * 0.222 = 0.2109
    P(test+ and not pregnant) = 0.20 * 0.778 = 0.1556
    P(test+) = 0.3665
    P(pregnant | test+) ≈ 0.5754

    P(test− and pregnant) = 0.05 * 0.222 = 0.0111
    P(test− and not pregnant) = 0.80 * 0.778 = 0.6224
    P(test−) = 0.6335
    P(pregnant | test−) ≈ 0.0175

    After second positive:
    new prior ≈ 0.5754
    P(test+ updated) ≈ 0.6315
    P(pregnant | two positives) ≈ 0.8656

    Odds correct positive result ≈ 0.2109 / 0.1556 ≈ 1.356
    Odds correct negative result ≈ 0.6224 / 0.0111 ≈ 56.054
    Odds ratio ≈ 1.356 / 56.054 ≈ 0.024

High-risk issues:

| Issue | Why high risk |
|---|---|
| confusing sensitivity with PPV | core conceptual error |
| not using false positive rate | wrong denominator |
| treating second test as same calculation with original prior | wrong updating |
| no odds/odds ratio in Q4 | misses explicit marking requirement |
| saying positive test is convincing | contradicts posterior ≈ 0.575 |

Good conclusion style:

    A positive test increases the probability of pregnancy from 0.222 to about 0.575, but this is not fully convincing because the false-positive rate is high. A negative test is much more informative, reducing the probability of pregnancy to about 0.018. The kit is sensitive but not very specific, so it produces too many false positives.

---

### 8.3 T1D drug / paired before-after treatment

Question pattern:

- blood glucose measured before and after injection;
- same mice measured twice;
- treatment has 3 levels: Vehicle, 1 mg/ml, 5 mg/ml;
- some mice died and have missing after-measurements;
- ask whether drug is useful.

Expected method:

    Compute within-subject Difference = before - after or after - before consistently.
    Analyze Difference ~ Treatment using one-way ANOVA if assumptions fit.
    Kruskal-Wallis acceptable but inferior unless justified.
    Mixed-effects two-way ANOVA can be acceptable but is harder and not required.

Must check:

1. data imported and checked:
   - `head()`;
   - `anyNA()`;
   - `anyDuplicated()`;
2. missing values investigated, not blindly dropped;
3. missing after-values linked to dead mice / toxicity discussed;
4. data reshaped wide so before and after are paired;
5. treatment converted to ordered factor:
   - Vehicle < 1 mg/ml < 5 mg/ml;
6. difference score calculated;
7. plot shows difference by treatment or paired before-after values;
8. method choice justified:
   - one factor Treatment with 3 levels;
   - response is within-subject change;
9. hypotheses stated for group means of Difference;
10. assumptions checked:
   - independence;
   - residual normality;
   - variance equality;
11. one-way ANOVA run;
12. post-hoc optional/descriptive if main effect not significant;
13. effect size and power discussion if asking next step;
14. toxicity/death discussed.

Reference values from marking guide:

    Bartlett p ≈ 0.6861
    Shapiro-Wilk p ≈ 0.4422
    ANOVA: F ≈ 2.601, p ≈ 0.102
    Tukey:
      1 mg/ml - Vehicle ≈ -0.18125, p ≈ 0.893
      5 mg/ml - Vehicle ≈ 0.59250, p ≈ 0.322
      5 mg/ml - 1 mg/ml ≈ 0.77375, p ≈ 0.0969
    eta² ≈ 0.224
    Cohen's f ≈ 0.537
    current power ≈ 0.514
    n per group for 0.8 power ≈ 12.2

High-risk issues:

| Issue | Why high risk |
|---|---|
| treats before and after as independent | major statistical design error |
| drops NAs without noting deaths | misses toxicity and cleaning marks |
| ignores Difference variable | does not preserve pairing |
| concludes drug works strongly despite p = 0.102 | overclaim |
| says "increase sample size" without power | weak next-step mark |
| ignores toxicity | misses key biological discussion |

Good conclusion style:

    The 5 mg/ml group showed the largest glucose reduction, but the ANOVA did not reach the conventional significance level. The effect size was still relatively large, and the study appeared underpowered, so the result suggests possible efficacy rather than definitive evidence. However, deaths in treated mice indicate toxicity, so future work should optimize administration and perform toxicity testing before increasing dose or sample size.

---

### 8.4 ToothGrowth / vitamin C formulation

Question pattern:

- response: tooth length `len`;
- predictors: supplement `supp`, dose `dose`;
- 2 × 3 factorial design;
- question asks whether new vitamin C formulation is useful.

Expected method:

    Two-way ANOVA with interaction: len ~ supp * dose

Must check:

1. data imported and checked:
   - `head()`;
   - `str()`;
   - missing/duplicates checked;
2. irrelevant index column removed if present;
3. `supp` converted to factor;
4. `dose` converted to ordered factor with levels 0.5, 1, 2;
5. plot shows both supplement and dose in one informative graph;
6. method choice:
   - continuous response;
   - two categorical predictors;
   - interaction relevant;
7. hypotheses stated for supp, dose, and interaction;
8. assumptions checked with residual plots or one coherent method;
9. two-way ANOVA with interaction run;
10. TukeyHSD included;
11. interaction interpreted;
12. high-dose equivalence discussed;
13. next step specific, not generic sample size.

Reference values:

    supp p ≈ 0.000241
    dose p < 2e-16
    supp:dose p ≈ 0.023683

    VC - OJ ≈ -1.490 mm, p ≈ 0.0002408
    2 - 0.5 dose difference ≈ 6.2595 mm, p ≈ 0
    VC:2 - OJ:2 ≈ 0.021 mm, p = 1.0

    Dose effect R² / eta-type contribution ≈ 0.703
    Interaction contribution ≈ 0.031

High-risk issues:

| Issue | Why high risk |
|---|---|
| uses one-way ANOVA only | inferior because factorial design ignored |
| ignores interaction | misses key biological conclusion |
| treats dose as continuous without justification | may be accepted only if explained but not ideal |
| says VC is always worse | misses high-dose equivalence |
| says formula is fully useful | overclaims lower-dose performance |
| no TukeyHSD | misses post-hoc marks |

Good conclusion style:

    Supplement type, dose, and their interaction all affected tooth length. The new formulation was generally inferior to orange juice at lower doses, but at the highest dose it was not detectably different from orange juice. Therefore, the formula may substitute natural vitamin C only at sufficiently high dose. Next steps should test long-term stability, toxicity, bioavailability, and possibly a very-low-dose or no-treatment reference group.

---

### 8.5 Mutation and survival / Mendelian inheritance

Question pattern:

- cross: Gene_X WT/mut × WT/mut;
- observed newborn genotype, sex, birth date;
- ask whether mutation affects survival.

Expected method:

    Chi-square goodness-of-fit against expected sex × genotype probabilities.

Expected distribution:

- sex: 50/50;
- genotype: WT:het:mut = 1:2:1;
- combined sex × genotype probabilities:
  - female WT = 0.125;
  - female het = 0.25;
  - female mut = 0.125;
  - male WT = 0.125;
  - male het = 0.25;
  - male mut = 0.125.

Must check:

1. data imported and checked;
2. ID and BD recognized as secondary unless used for extra description;
3. sex and genotype values checked;
4. sex × genotype table made;
5. counts and percentages described;
6. expected Mendelian distribution stated;
7. expected counts calculated;
8. chi-square goodness-of-fit used, not independence test;
9. assumptions checked:
   - categorical;
   - independent observations;
   - mutually exclusive cells;
   - expected counts sufficient;
10. hypotheses stated:
   - H0: data follow expected distribution;
   - H1: data do not follow expected distribution;
11. residuals/cell deviations interpreted;
12. underrepresentation of homozygous mutants discussed;
13. sex-dependent pattern discussed if visible;
14. biological next step proposed.

Reference observed table:

    female: WT 7, het 26, mut 5
    male:   WT 10, het 30, mut 2

Expected counts for n = 80:

    female WT 10, female het 20, female mut 10
    male WT 10, male het 20, male mut 10

Reference result:

    χ² ≈ 16.6
    df = 5
    p ≈ 0.005324

High-risk issues:

| Issue | Why high risk |
|---|---|
| uses chi-square independence | wrong question; this is observed vs expected |
| collapses genotype only when marking expects sex × genotype | misses sex-specific distribution |
| forgets expected probabilities | cannot justify GOF |
| says "mutation affects genotype" | imprecise; mutation likely affects survival/viability |
| no discussion of mutant underrepresentation | misses biological interpretation |

Good conclusion style:

    The observed sex × genotype distribution deviated from the expected Mendelian distribution. Homozygous mutant mice were underrepresented, suggesting reduced survival or viability, possibly during embryonic or early postnatal development. Follow-up should identify the developmental stage of loss, tissues expressing Gene_X, and whether surviving mutants have compensatory mechanisms.

---

### 8.6 Coffee shop opening hours / non-independent proportions

Question pattern:

- early opening: satisfied 864, unsatisfied 714;
- later opening: satisfied 980, unsatisfied 473;
- responses are iPad button presses;
- same customer may press repeatedly or appear in both periods.

Expected method:

    Bootstrap comparison of satisfaction proportions.
    Chi-square/Fisher should be rejected as primary because independence is broken.

Must check:

1. independence problem explicitly stated;
2. categories/samples not treated as unique students;
3. chi-square/Fisher rejected as primary method;
4. null and alternative hypotheses stated for satisfaction proportion;
5. early and late proportions calculated;
6. bootstrap performed;
7. CI or non-overlap shown;
8. direction stated:
   - later opening has higher satisfaction;
9. limitation discussed:
   - button presses, not unique students;
   - same students may appear in both periods;
10. optional chi-square only as secondary/descriptive if method-choice penalty already applied.

Reference proportions:

    early satisfaction = 864 / (864 + 714) ≈ 0.548
    late satisfaction = 980 / (980 + 473) ≈ 0.674
    difference ≈ 0.126 in favor of later opening

Marking guide also reports chi-square if used:

    χ² ≈ 50.629
    df = 1
    p ≈ 1.116e-12

High-risk issues:

| Issue | Why high risk |
|---|---|
| says observations are independent | core method error |
| uses chi-square as primary without caveat | loses method-choice marks |
| reports significance but not direction | partial mark loss |
| says "students prefer" without caveat | overclaims because data are button presses |
| no bootstrap CI | misses expected analysis |

Good conclusion style:

    The bootstrap results support a higher proportion of satisfied responses during later opening hours. However, the data are button presses rather than unique student-level responses, so the conclusion should be framed as higher recorded satisfaction responses, not a definitive survey of individual student preference.

---

## 9. High-risk ADS2 trap bank

### 9.1 "Counts mean chi-square" trap

Wrong:

    These are counts, so chi-square is appropriate.

Correct audit:

    Counts only justify chi-square if observations are independent and categories are mutually exclusive. If repeated button presses or overlapping samples are possible, chi-square is not the primary method.

### 9.2 "Before-after independent groups" trap

Wrong:

    Compare before and after as two independent groups.

Correct audit:

    Same subjects are measured twice, so the analysis must preserve pairing, usually by calculating within-subject differences.

### 9.3 "Endpoint trend" trap

Wrong:

    The first and last values differ, so there is a trend.

Correct audit:

    Trend should be estimated with a regression slope using all observations.

### 9.4 "No post-hoc after ANOVA" trap

Wrong:

    ANOVA is significant, therefore all groups differ.

Correct audit:

    ANOVA only shows at least one difference. Post-hoc comparisons are needed to identify which groups differ.

### 9.5 "Accept null" trap

Wrong:

    We accept H0 and conclude no effect.

Correct audit:

    Say "the data did not provide clear evidence of an effect"; do not claim the effect is absent.

### 9.6 "Generic next step" trap

Wrong:

    Increase the sample size.

Correct audit:

    If increasing sample size is suggested, justify it using power analysis or a specific uncertainty.

### 9.7 "Bayes conditioning" trap

Wrong:

    Sensitivity is the chance of being pregnant after a positive test.

Correct audit:

    Sensitivity is P(test+ | pregnant). PPV/posterior is P(pregnant | test+), requiring Bayes' theorem.

---

## 10. Scoring-oriented audit rubric

When asked to estimate marks, use cautious ranges instead of pretending to be the official marker.

### 10.1 Per-section scoring

| Section | Full-credit signs | Common losses |
|---|---|---|
| Import/check | import + head/str/NA/duplicates + cleaning | no checks, silent cleaning |
| Plot/table | informative, labels, units, primary data/model | poor labels, wrong plot, no units |
| Method | matches design and variables | wrong test, pairing ignored |
| Justification | assumptions named and checked | assumptions listed but not interpreted |
| Hypotheses | symmetrical, parameter-based | vague or reversed H0/H1 |
| Analysis | correct test/model + output | missing output or wrong model |
| Post-hoc/effect | included when needed | ANOVA significant but no follow-up |
| Results | statistic + p + estimate/CI/effect | p-only reporting |
| Discussion | answers question + limitation + specific next step | overclaim or generic sample size |
| Rmd | knits, reproducible, readable | cut-off code, comments as prose |

### 10.2 Estimated readiness labels

| Verdict | Meaning |
|---|---|
| **Ready** | only low-risk polish remains |
| **Almost ready** | no major statistical errors, but some medium fixes needed |
| **Needs revision** | one or more high-risk issues could lose major marks |
| **Not ready** | wrong method, missing core components, or knit/reproducibility failure |

---

## 11. Audit response templates

### 11.1 Full PDF audit template

    ## Audit summary
    Overall risk: [Low / Medium / High]
    Submission readiness: [Ready / Almost ready / Needs revision / Not ready]

    The strongest part is [area]. The highest-risk issue is [issue].

    ## Audit table
    | Severity | Location | Issue | Why it matters | Concrete fix |
    |---|---|---|---|---|

    ## Must fix
    1. ...

    ## Should fix
    1. ...

    ## Optional polish
    1. ...

    ## Estimated marking impact
    - Statistical correctness: ...
    - Marking-guide coverage: ...
    - R Markdown reproducibility: ...
    - Writing clarity: ...

    ## Final verdict
    [Submit / revise before submitting / major revision needed]

### 11.2 One-question audit template

    ## Q[number] audit
    Overall risk: [Low / Medium / High]

    | Severity | Issue | Fix |
    |---|---|---|

    ### Marking-guide coverage
    - Import/check: [complete / partial / missing]
    - Plot/table: [complete / partial / missing]
    - Method choice: [correct / questionable / wrong]
    - Hypotheses: [complete / partial / missing]
    - Assumptions: [complete / partial / missing]
    - Main result: [complete / partial / missing]
    - Discussion: [complete / partial / missing]

    ### Priority fixes
    1. ...

### 11.3 Rmd knit-safety audit template

    ## Knit-safety audit
    | Severity | Location | Issue | Fix |
    |---|---|---|---|

    ### Main knit risks
    1. Missing package/load order problem
    2. Long lines cut off in PDF
    3. Text written as comments
    4. File path/anonymity issue
    5. Chunk option issue

---

## 12. Final self-audit before replying

Before giving the audit, check:

1. Did I prioritize high-risk statistical errors first?
2. Did I separate statistical issues from formatting issues?
3. Did I tie comments to marking-guide marks where possible?
4. Did I give concrete fixes rather than vague criticism?
5. Did I avoid rewriting the whole answer unless asked?
6. Did I check paired/repeated/non-independent designs?
7. Did I check whether the correct post-hoc/effect size/CI was included?
8. Did I check R Markdown reproducibility and PDF line overflow?
9. Did I preserve the user's structure and wording where possible?
10. Did I give a clear readiness verdict?
