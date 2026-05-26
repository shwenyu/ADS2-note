---
name: ads2-exam-triage-method-selection
description: Read an ADS2 coding challenge question set and decide the correct statistical route for each question before any Rmd answer is written. Use this when the user provides exam questions, mock tasks, dataset descriptions, variable descriptions, or asks "what method should I use?".
---

# ADS2 Skill 1 — Exam Triage & Method Selection

## 0. Purpose

This Skill performs **question interpretation, method selection, and analysis-route planning**.

It must not generate a full R Markdown answer unless explicitly asked. Its job is to prevent the most expensive ADS2 mistake: choosing the wrong statistical method before coding.

Main outputs:

1. identify the response variable;
2. identify explanatory variable(s);
3. classify variable types;
4. infer study design;
5. identify the target task;
6. choose the correct statistical method;
7. flag special cases where ordinary tests are inappropriate;
8. produce a concise route map for each question;
9. outline the minimum EDA, assumption checks, model/test code direction, and interpretation structure.

---

## 1. When to use

Use this Skill when the user provides:

- a full ADS2 mock exam;
- one ADS2 coding challenge question;
- a dataset description;
- a marking guide excerpt;
- variable names and question text;
- a request such as:
  - "这题用什么 test?"
  - "帮我解读题目方向"
  - "先判断每题的方法"
  - "这题是不是只能 bootstrap / Bayes / simulation?"
  - "帮我规划 data analysis 思路"
  - "这题 Rmd 应该怎么展开?"

---

## 2. Do NOT do these things

Do not:

1. write the full Rmd answer unless explicitly asked;
2. invent numerical results;
3. run a test before selecting the method;
4. assume all count data can use chi-square;
5. assume all group comparisons can use t-test / ANOVA;
6. ignore non-independence, repeated observations, paired design, or overlapping samples;
7. collapse important variables unless justified;
8. write "increase sample size" as the default next step;
9. report p-values without effect size, uncertainty, and interpretation;
10. use endpoint difference alone when the question asks for trend;
11. use ordinary linear regression when the response distribution clearly violates its assumptions and a GLM route is more appropriate.

---

## 3. Required extraction for every question

For each question, extract:

1. **Response variable**
   - continuous;
   - categorical;
   - binary;
   - count/frequency;
   - proportion;
   - paired difference;
   - time trend;
   - probability/event;
   - matrix/vector outcome;
   - cluster label or unsupervised structure.

2. **Explanatory variable(s)**
   - categorical;
   - continuous;
   - ordered factor;
   - time/date;
   - treatment group;
   - repeated measurement;
   - theoretical expected category;
   - grouping/blocking variable;
   - interaction candidate;
   - predictor matrix.

3. **Study design**
   - independent groups;
   - paired / before-after;
   - repeated measures;
   - overlapping samples;
   - theoretical expected proportions;
   - factorial design;
   - dose-response;
   - time trend;
   - diagnostic probability;
   - observational association;
   - predictive modelling;
   - simulation/bootstrap;
   - Bayesian update;
   - clustering / unsupervised exploration.

4. **Target task**
   - compare group means;
   - compare proportions;
   - test observed vs expected counts;
   - test association between categorical variables;
   - quantify trend / slope;
   - prediction;
   - estimate uncertainty;
   - Bayesian update;
   - bootstrap / simulation inference;
   - clustering / unsupervised exploration;
   - model comparison;
   - interpretation of uncertainty.

5. **Risk flags**
   - non-independence;
   - repeated button presses;
   - same subject can contribute multiple observations;
   - groups overlap;
   - categories are not mutually exclusive;
   - missingness may be biologically meaningful;
   - paired structure;
   - sparse cells;
   - outliers;
   - non-normal residuals;
   - unequal variance;
   - non-linearity;
   - extrapolation risk;
   - trend incorrectly tempted by endpoint difference;
   - multiple testing;
   - confounding;
   - small sample size;
   - model overfitting;
   - wrong probability conditioning direction.

---

## 4. Output format

Return a table first.

| Q | Response | Predictors | Data structure | Target task | Recommended method | Avoid | Special notes |
|---|---|---|---|---|---|---|---|

Then give a short explanation for each question:

Q1 route:
Data structure → candidate methods → chosen method → why alternatives are weaker or invalid → minimum R direction → interpretation focus.

End with a **Method Route Summary**:

Q1 → two-way ANOVA with interaction  
Q2 → chi-square goodness-of-fit  
Q3 → bootstrap comparison of proportions  
Q4 → linear regression slope + 95% CI  
Q5 → Bayesian update / conditional probability table

---

## 5. Global ADS2 analysis workflow

For every question, follow this route unless the task is purely mathematical probability.

### 5.1 Understand the question

1. Translate the question into:
   - response;
   - predictor(s);
   - design;
   - target claim.
2. Identify whether the question asks for:
   - description;
   - comparison;
   - association;
   - prediction;
   - trend;
   - probability;
   - uncertainty;
   - classification / clustering.
3. Decide whether the answer should be:
   - a formal test;
   - a model;
   - a bootstrap interval;
   - a simulation;
   - a Bayesian calculation;
   - an exploratory visualization only.

### 5.2 Inspect and clean data

Minimum checks:

- use `str()`, `glimpse()`, `summary()`, `head()`;
- confirm variable classes;
- convert categorical variables to factors when appropriate;
- check missing values;
- check impossible values;
- check duplicated or repeated observations;
- check whether each row is one independent experimental unit.

Core idea:

Data cleaning is not cosmetic. It determines whether the statistical unit is a person, sample, button press, time point, genotype, or group summary.

### 5.3 Exploratory visualization

Choose plot by data structure:

| Data structure | Preferred EDA |
|---|---|
| continuous response by group | boxplot, violin plot, mean + CI |
| two categorical variables | bar plot, mosaic plot, proportion table |
| continuous x and continuous y | scatterplot + trend line |
| time series | line plot over time |
| counts over categories | observed vs expected bar plot |
| model residuals | residual vs fitted, QQ plot |
| probability problem | probability tree or table |
| clustering | pair plot, PCA plot, cluster plot |

EDA should answer:

1. What is the pattern?
2. Are assumptions plausible?
3. Are there outliers or sparse categories?
4. Does the chosen method still match the question?

### 5.4 Choose method before coding

Use the decision rule:

Variable types + study design + target claim = method

Never choose based only on the function name you remember.

### 5.5 Run analysis

Minimum structure:

1. state hypotheses or modelling goal;
2. fit test/model/simulation;
3. extract estimate, uncertainty, and p-value/posterior probability where relevant;
4. check assumptions or justify robust alternative;
5. interpret in context.

### 5.6 Report results

A good ADS2 answer includes:

- method name;
- why the method fits the data;
- key estimate/effect size;
- uncertainty interval;
- p-value or posterior probability when relevant;
- assumption caveat;
- context-based conclusion.

Avoid:

- "p < 0.05, therefore significant" only;
- "accept the null";
- unqualified causal language for observational data;
- overinterpreting noisy plots;
- giving code output without biological/statistical interpretation.

---

## 6. Method-selection decision rules

### 6.1 Continuous response + categorical predictor(s)

Use when outcome is numeric.

- 1 categorical predictor, 2 independent groups → independent t-test if assumptions fit.
- 1 categorical predictor, ≥ 3 groups → one-way ANOVA.
- 2 categorical predictors → two-way ANOVA with interaction.
- 2 paired groups → paired t-test or paired difference analysis.
- Repeated before-after with treatment groups → calculate within-subject difference, then compare `Difference ~ Treatment`.
- Assumptions poor → bootstrap / Wilcoxon / Kruskal-Wallis, depending on structure.

Default ADS2 preference:

If factorial design exists, test the interaction unless there is a clear reason not to.

Minimum R direction:

- `boxplot(y ~ group, data = df)`
- `model <- lm(y ~ group, data = df)`
- `anova(model)`
- `TukeyHSD(aov_model)` if post-hoc pairwise comparison is needed
- residual checks: QQ plot + residual vs fitted

Interpretation focus:

- mean differences;
- confidence intervals;
- interaction pattern if present;
- whether group differences are practically meaningful.

---

### 6.2 ANOVA with interaction

Use when:

- response is continuous;
- there are two categorical predictors;
- the question asks whether the effect of one factor depends on the other.

Route:

1. plot group means by both factors;
2. fit `lm(y ~ factor1 * factor2, data = df)`;
3. inspect interaction first;
4. if interaction is meaningful, interpret simple effects rather than only main effects;
5. if no interaction, interpret main effects cautiously.

Avoid:

- reducing factorial design to multiple t-tests;
- ignoring interaction;
- treating ordered dose as numeric unless the question asks for linear dose-response.

High-risk example:

ToothGrowth / vitamin C:
response = `len`; predictors = `supp` and `dose`; use two-way ANOVA with interaction: `len ~ supp * dose`. Treat `dose` as an ordered factor unless regression is explicitly requested.

---

### 6.3 Observed counts vs theoretical proportions

Use chi-square goodness-of-fit.

Examples:

- Mendelian 1:2:1;
- sex 50/50;
- expected distribution table;
- observed counts vs theoretical ratio.

Route:

1. define observed counts;
2. define expected probabilities;
3. check probabilities sum to 1;
4. compute expected counts;
5. run goodness-of-fit test;
6. inspect which categories contribute most to deviation.

Important:

If the expected structure is sex × genotype, do not collapse sex unless the question only asks genotype.

High-risk example:

WT/mut × WT/mut:
expected genotype ratio = WT:het:mut = 1:2:1.
If sex is included and expected 50/50, expected sex × genotype probabilities are:
0.125, 0.25, 0.125, 0.125, 0.25, 0.125.

---

### 6.4 Two categorical variables

Use chi-square test of independence only if:

1. observations are independent;
2. categories are mutually exclusive;
3. each observation contributes once;
4. expected counts are mostly ≥ 5.

Route:

1. create contingency table;
2. convert counts to row/column proportions;
3. inspect expected counts;
4. choose chi-square or Fisher/exact method if sparse;
5. report association and direction using proportions.

Do not use chi-square if:

- same person can respond multiple times;
- button presses are counted instead of people;
- samples overlap between conditions;
- groups are not mutually exclusive.

---

### 6.5 Non-independent proportions

If the data look like a 2×2 table but independence is violated, use bootstrap or simulation.

Red flags:

- same customer/student may press multiple times;
- same people may appear in early and late groups;
- categories are not exclusive;
- repeated responses;
- self-selected button presses.

Correct route:

Reject chi-square / Fisher as the primary method.
Use bootstrap proportions or bootstrap difference in proportions.

Minimum R direction:

- resample at the correct unit level;
- compute statistic each resample;
- build bootstrap distribution;
- report percentile CI or bootstrap p-value if appropriate.

Interpretation focus:

- difference in proportions;
- uncertainty from resampling;
- limitation from non-independent sampling.

---

### 6.6 Continuous predictor + continuous response

Use linear regression.

Required if the question asks:

- relationship;
- prediction;
- trend;
- "how much changed over time";
- "rate of change";
- "effect of dose/year".

Critical rule:

Quantify trend using `lm(response ~ time)` slope + 95% CI, not endpoint difference.

Route:

1. scatterplot;
2. fit linear model;
3. check residuals and linearity;
4. report slope, CI, R-squared if useful;
5. interpret slope in original units.

Avoid:

- interpreting correlation as causation;
- extrapolating outside observed x range;
- using only correlation when the question asks for prediction or change per unit x.

---

### 6.7 Correlation

Use correlation when the target is strength/direction of association between two continuous variables, not prediction.

Choose:

- Pearson correlation for approximately linear association and no severe outliers;
- Spearman correlation for monotonic non-linear association or ordinal/non-normal data.

Report:

- correlation coefficient;
- confidence interval or p-value if required;
- scatterplot-based interpretation.

Avoid:

- treating correlation as slope;
- assuming causation;
- ignoring outliers that drive the relationship.

---

### 6.8 Time series / time trend

Use when observations are ordered over time.

Route:

1. plot variable against time;
2. identify trend, seasonality, irregular jumps, missing time points;
3. decide whether the question needs:
   - descriptive trend;
   - linear slope;
   - smoothing;
   - forecasting;
   - comparison before/after intervention.
4. use regression for simple trend if appropriate;
5. avoid endpoint-only comparison unless explicitly asked.

Minimum R direction:

- convert time variable correctly;
- `ggplot(df, aes(time, y)) + geom_line()`;
- `lm(y ~ time, data = df)` for simple slope;
- consider moving average/smoothing only if the task is descriptive.

Interpretation focus:

- rate of change;
- uncertainty;
- whether trend is stable or driven by a few points.

---

### 6.9 Generalised linear models

Use GLM when response type is not well modelled by ordinary linear regression.

| Response type | Candidate model | Typical link |
|---|---|---|
| binary outcome | logistic regression | logit |
| count outcome | Poisson regression | log |
| overdispersed count | quasi-Poisson / negative binomial | log |
| proportion with trials | binomial GLM | logit |

Route:

1. identify response distribution;
2. choose family and link;
3. fit model;
4. inspect overdispersion where relevant;
5. report effect as odds ratio, rate ratio, or probability change when useful.

Avoid:

- using linear regression for binary outcomes;
- treating count data as normal by default;
- interpreting log-odds directly without explanation.

---

### 6.10 Matrix-based modelling

Use when the roadmap/task frames data as matrices or design matrices.

Core idea:

A linear model can be represented as:

Y = Xβ + ε

Where:

- Y = response vector;
- X = design matrix / predictor matrix;
- β = coefficients;
- ε = residual error.

Use this thinking to understand:

- intercept;
- dummy variables;
- fitted values;
- residuals;
- matrix multiplication;
- why categorical variables become columns.

Route:

1. identify Y;
2. identify X;
3. map each coefficient to a biological/statistical meaning;
4. connect model formula to design matrix;
5. interpret coefficients in context.

Avoid:

- treating matrix notation as separate from regression;
- forgetting that factor coding changes coefficient meaning.

---

### 6.11 Bayesian / mathematical probability questions

If the question gives:

- sensitivity;
- specificity;
- prevalence;
- conditional probabilities;
- repeated tests;
- posterior probability;

then this is usually **mathematical probability / Bayesian update**, not a frequentist test.

Use:

- probability table;
- Bayes' theorem;
- PPV / NPV;
- repeated posterior-as-prior update.

Route:

1. define events clearly;
2. identify prior/base rate;
3. identify likelihood/sensitivity/specificity;
4. compute numerator and denominator;
5. interpret posterior probability.

Critical rule:

Always check conditioning direction:
P(Disease | Positive) is not the same as P(Positive | Disease).

Avoid:

- base-rate neglect;
- mixing up false positive rate and specificity;
- reporting probability without defining the condition.

---

### 6.12 Bayesian inference

Use when the question asks for posterior distribution, prior, likelihood, or Bayesian updating.

Core terms:

- prior = belief before data;
- likelihood = data evidence under parameter values;
- posterior = updated belief after data;
- credible interval = Bayesian probability interval for parameter.

Route:

1. define parameter;
2. define prior;
3. define likelihood;
4. compute or simulate posterior;
5. summarize posterior mean/median and credible interval;
6. answer in probability language.

Avoid:

- calling credible intervals "confidence intervals";
- ignoring prior sensitivity;
- using Bayesian language for ordinary p-values.

---

### 6.13 Bootstrap / simulation inference

Use bootstrap when analytical assumptions are weak or the sampling distribution is difficult to derive.

Typical tasks:

- confidence interval for median;
- difference in means/proportions with non-normal data;
- non-independent or unusual sampling route;
- small sample uncertainty;
- custom statistic.

Route:

1. define the statistic;
2. resample at the correct unit level;
3. repeat many times;
4. visualize bootstrap distribution;
5. report percentile CI or simulation probability.

Key distinction:

Bootstrap resamples observed data.
Simulation generates data from an assumed model.

Avoid:

- resampling rows when the independent unit is subject/group;
- using bootstrap to fix biased data collection;
- ignoring the original study design.

---

### 6.14 Clustering / unsupervised learning

Use only if the question explicitly asks for grouping, clustering, unsupervised structure, or pattern discovery.

Check:

- numeric variables need scaling;
- distance metric must be justified;
- number of clusters needs rationale;
- biological interpretation must be cautious;
- cluster validation / stability should be discussed.

Route:

1. select relevant numeric variables;
2. scale variables;
3. choose distance metric;
4. run clustering or PCA;
5. visualize clusters;
6. interpret as exploratory, not confirmatory.

Avoid:

- treating clusters as proven biological classes;
- clustering variables with incomparable scales without standardization;
- overinterpreting arbitrary cluster number.

---

## 7. ADS2 high-risk traps

### Trap 1 — ToothGrowth / vitamin C

If response = `len`, predictors = `supp` and `dose`:

Use two-way ANOVA with interaction: `len ~ supp * dose`.
Treat dose as ordered factor unless the question explicitly asks regression.

Do not reduce to one-way ANOVA unless justified.

---

### Trap 2 — Mendelian genotype

If crossing `WT/mut × WT/mut`:

Expected genotype ratio = WT:het:mut = 1:2:1.

If sex is included and expected 50/50, expected sex × genotype probabilities are:

- female WT = 0.125;
- female het = 0.25;
- female mut = 0.125;
- male WT = 0.125;
- male het = 0.25;
- male mut = 0.125.

Use chi-square goodness-of-fit.

---

### Trap 3 — Coffee shop button presses

If counts are button presses and customers can respond repeatedly:

Do not use chi-square or Fisher as primary method.
Use bootstrap comparison of satisfaction proportions.

---

### Trap 4 — Paired before-after treatment

If same subject measured before and after:

Do not treat before and after as independent groups.
Calculate Difference = after - before.
Then analyse Difference by treatment.

---

### Trap 5 — Trend over time

If asked "how much changed over time":

Do not use endpoint difference alone.
Use `lm(response ~ time)`, report slope and 95% CI.

---

### Trap 6 — Binary outcome

If response is yes/no, survived/died, diseased/not diseased:

Do not use ordinary linear regression as default.
Use logistic regression or a proportion test depending on the question design.

---

### Trap 7 — Count outcome

If response is number of events:

Do not assume normality.
Consider Poisson or negative binomial route; check overdispersion.

---

### Trap 8 — Probability wording

If the question asks "given positive test, what is the probability of disease":

Do not use sensitivity alone.
Use Bayes' theorem with prevalence/base rate.

---

## 8. Exam-answer structure templates

### 8.1 Method selection answer

Use this when the user asks "which method?"

1. Response variable: ...
2. Predictor(s): ...
3. Data structure: ...
4. Target task: ...
5. Recommended method: ...
6. Why this method: ...
7. Avoid: ...
8. Minimum R route: ...
9. Final interpretation should focus on: ...

---

### 8.2 Rmd analysis answer

Use this when the user asks for a full coding route.

1. Question restatement
2. Data inspection
3. Data cleaning / factor conversion
4. EDA plot
5. Hypotheses or modelling aim
6. Model/test code
7. Assumption check or robustness note
8. Result extraction
9. Interpretation in context
10. Limitation / caveat

---

### 8.3 Hypothesis-test wording

For frequentist tests:

- H0: no difference / no association / coefficient = 0.
- H1: difference / association / coefficient ≠ 0, or directional if specified.
- Decision: based on p-value and alpha.
- Interpretation: evidence for/against effect in context, not proof.

Avoid "accept H0".
Use "fail to reject H0" or "insufficient evidence".

---

### 8.4 Model interpretation wording

For regression/GLM:

- Coefficient means expected change in response for one-unit increase in predictor, holding other variables constant.
- For logistic regression, exponentiated coefficient is odds ratio.
- For Poisson regression, exponentiated coefficient is rate ratio.
- Confidence interval describes uncertainty of estimate.
- Prediction should stay within observed data range.

---

### 8.5 Bootstrap wording

For bootstrap:

- The statistic of interest is ...
- We resample at the level of ...
- The bootstrap distribution approximates sampling variability of ...
- The 95% bootstrap CI is interpreted as plausible values for ...
- Limitations: bootstrap reflects the observed sample and cannot fix biased sampling.

---

### 8.6 Bayesian wording

For Bayesian inference:

- Prior represents initial belief.
- Likelihood represents data evidence.
- Posterior combines prior and likelihood.
- A 95% credible interval means there is 95% posterior probability that the parameter lies in the interval, conditional on the model and prior.

---

## 9. Quick decision table

| Question pattern | Recommended route | Avoid |
|---|---|---|
| numeric outcome, 2 independent groups | t-test / linear model | paired test unless paired |
| numeric outcome, ≥3 groups | one-way ANOVA | repeated t-tests |
| numeric outcome, 2 factors | two-way ANOVA with interaction | ignoring interaction |
| before-after same subject | paired analysis / difference score | independent t-test |
| observed counts vs expected ratio | chi-square goodness-of-fit | chi-square independence |
| two categorical variables, independent rows | chi-square independence | using if repeated responses |
| sparse categorical table | Fisher/exact or simulation | blind chi-square |
| non-independent proportions | bootstrap difference in proportions | chi-square/Fisher as primary |
| continuous x and y | correlation or regression | choosing without target |
| change over time | regression slope / time-series plot | endpoint-only difference |
| binary outcome with predictors | logistic regression | ordinary linear regression |
| count outcome with predictors | Poisson / negative binomial GLM | t-test/linear regression by default |
| sensitivity/specificity/prevalence | Bayes theorem | confusing P(A|B) with P(B|A) |
| posterior/prior/likelihood | Bayesian inference | p-value language |
| unknown sampling distribution | bootstrap / simulation | overclaiming parametric test |
| clustering requested | scaled clustering/PCA | confirmatory causal claims |

---

## 10. Self-audit before output

Check:

- Did I identify the response variable?
- Did I identify all predictors?
- Did I classify variable types correctly?
- Did I detect paired/repeated/non-independent design?
- Did I distinguish goodness-of-fit from independence chi-square?
- Did I distinguish correlation from regression?
- Did I distinguish linear regression from GLM?
- Did I flag mathematical/Bayesian questions separately from statistical tests?
- Did I avoid writing full Rmd code unless asked?
- Did I clearly state what method each question should use?
- Did I include the minimum EDA and assumption-check route?
- Did I explain what to avoid and why?
