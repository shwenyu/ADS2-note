---
name: ads2-exam-triage-method-selection
description: Read an ADS2 coding challenge question or question set and choose the correct statistical route before any Rmd answer is written. Use when the user asks what method/test/model to use, or provides exam questions, dataset descriptions, variables, or marking-guide excerpts.
---

# ADS2 Skill 1 — Exam Triage & Method Selection

## 0. Purpose

This Skill does **method triage** only: read the question, identify the data structure, choose the correct analysis route, and flag high-risk traps before coding.

It should **not** generate a full Rmd answer unless explicitly asked.

Core principle:

> **Response type + predictor type + study design + target claim = method.**  
> Do not choose a test only because a familiar function name appears in memory.

Bootstrap fallback principle:

> **Bootstrap is allowed when the prompt blocks ordinary assumptions.**  
> If the population distribution is unknown, clearly non-normal, the total population/sample is very small, observations are not independent, categories are non-mutually exclusive, or all standard tests require assumptions not supported by the question, consider bootstrap / simulation.  
> Do **not** rescue a familiar test by adding hidden assumptions such as independence, mutual exclusivity, normality, or large-sample approximation.

---

## 1. Required output

Start with this table:

| Q | Response | Predictors | Design / unit | Target task | Recommended route | Avoid | Key risk |
|---|---|---|---|---|---|---|---|

Then give a concise route per question:

1. **Data structure** — response, predictors, independent unit.
2. **Candidate methods** — plausible options.
3. **Chosen method** — final recommendation.
4. **Why not alternatives** — especially invalid assumptions.
5. **Minimum R direction** — not full code, only key functions.
6. **Interpretation focus** — what the answer must explain.

End with:

~~~text
Method Route Summary
Q1 → ...
Q2 → ...
Q3 → ...
~~~

---

## 2. Do not do these

Do not:

1. invent numerical results;
2. write the full Rmd unless asked;
3. collapse variables before checking whether they define the expected structure;
4. use χ² only because the data are counts;
5. use t-test / ANOVA only because the outcome is numeric;
6. ignore paired, repeated, overlapping, or non-independent observations;
7. quantify trends with endpoint difference alone;
8. treat sensitivity/specificity questions as ordinary hypothesis tests;
9. write “increase sample size” as the default next step;
10. report p-values without estimate, uncertainty, and contextual interpretation.
11. assume independence, mutual exclusivity, normality, or adequate sample size unless the prompt supports it;
12. reject bootstrap merely because another test is familiar — if other methods require unsupported assumptions, bootstrap can be the safer route.

---

## 3. First-pass extraction checklist

For every question, extract:

### 3.1 Response variable

Classify as:

- continuous;
- binary;
- categorical;
- count / frequency;
- proportion;
- paired difference;
- time trend;
- conditional probability / event;
- matrix / multivariate outcome;
- cluster / unsupervised structure.

### 3.2 Predictor / explanatory structure

Classify as:

- categorical group;
- continuous predictor;
- ordered dose / time;
- treatment;
- before-after measurement;
- subject ID / repeated unit;
- theoretical expected ratio;
- blocking / subgroup variable;
- interaction candidate.

### 3.3 Study design and independent unit

Ask:

- What is one row?
- What is one independent unit?
- Can the same subject contribute more than once?
- Are samples paired, repeated, or overlapping?
- Is the task observed-vs-expected, group comparison, association, trend, prediction, or Bayesian update?
- Is any missingness biologically meaningful?

---

## 4. Core decision tree

~~~text
0. Assumption gate:
   If standard tests require assumptions the prompt does not justify
   (normality, large sample, independent observations, mutually exclusive categories,
   known population distribution), do not silently assume them.
   → Consider bootstrap / simulation when no ordinary method is defensible.

1. Is the task conditional probability / sensitivity / specificity / prevalence?
   → Bayes / mathematical probability, not a standard hypothesis test.

2. Is the response continuous?
   - categorical predictor, 2 independent groups → t-test / linear model
   - categorical predictor, ≥3 groups → one-way ANOVA
   - two categorical predictors → two-way ANOVA with interaction
   - paired before-after → paired test or Difference = after - before
   - continuous predictor → regression / correlation depending on target

3. Is the response count/frequency?
   - observed vs theoretical ratio → χ² goodness-of-fit
   - two categorical variables, independent rows → χ² independence
   - sparse expected cells → Fisher / exact / simulated χ²
   - repeated or overlapping counts → avoid ordinary χ²; consider bootstrap/simulation

4. Is the task comparing proportions?
   - independent person/sample-level observations → χ² / prop.test / binomial-style route
   - repeated events, votes, visits, button presses, overlapping samples → bootstrap/simulation and state limitation

5. Is the task trend/change over ordered time or dose?
   → regression slope + 95% CI; do not rely on endpoint difference alone.

6. Is the task clustering or unsupervised grouping?
   → scale numeric variables, choose distance/algorithm, visualize, interpret as exploratory.

7. Is every standard route blocked by unsupported assumptions?
   Examples:
   - unknown population distribution;
   - clearly non-normal distribution;
   - very small total population/sample;
   - lack of independence;
   - overlapping / repeated observations;
   - non-mutually-exclusive categories.
   → Use bootstrap / simulation as a fallback uncertainty route,
     while clearly stating the resampling unit and limitation.
~~~

---

## 5. General rules distilled from ADS2 marking logic

### Rule 1 — Count data are not automatically χ²

First identify what the counts are being compared against:

| Count structure | Route |
|---|---|
| observed counts vs theoretical expected distribution | χ² goodness-of-fit |
| two categorical variables with independent rows | χ² independence |
| small expected counts | Fisher / exact / simulated χ² |
| repeated / overlapping / non-exclusive counts | bootstrap / simulation / descriptive CI |

### Rule 2 — For goodness-of-fit, identify the **full expected table**

Do not collapse categories too early.

If the prompt provides an additional categorical variable that is part of the biological or experimental expectation, the expected distribution may be defined over the **combined table**.

Examples of expected structures:

| Prompt structure | Possible expected table |
|---|---|
| genotype only | genotype ratio |
| sex + genotype | sex ratio × genotype ratio |
| treatment + category | treatment-specific expected distribution |
| location + phenotype | location × phenotype distribution |

Use:

~~~text
Full expected structure first → collapse only if justified.
~~~

### Rule 3 — Factorial design means interaction first

If a continuous response has two categorical predictors, start from:

~~~r
response ~ factor1 * factor2
~~~

Interpretation logic:

1. check interaction;
2. if interaction matters, interpret simple effects / pairwise comparisons;
3. if interaction does not matter, interpret main effects cautiously.

Avoid multiple separate t-tests or ignoring interaction.

### Rule 4 — Paired / repeated data must preserve pairing

Red flags:

- same subject before and after;
- baseline/follow-up;
- left/right;
- repeated measurements;
- subject ID;
- paired samples.

Safe ADS2 route for before-after with treatment groups:

~~~text
reshape wide → Difference = after - before → analyse Difference by group
~~~

Do not treat before and after as independent groups.

### Rule 5 — Event-level counts may be pseudoreplication

For proportions, check whether `n` means **people/samples** or **events/responses**.

High-risk event units:

- button press;
- vote;
- visit;
- click;
- repeated customer;
- multiple responses from same person.

If rows/events are not independent, ordinary χ² / Fisher / prop.test can underestimate uncertainty. Prefer bootstrap/simulation if that is the best available route, and state that it estimates uncertainty for recorded responses, not perfectly independent individuals.

### Rule 6 — Trend/change requires slope, not endpoints

If the question asks “how much changed over time / dose / order”:

~~~r
model <- lm(response ~ time, data = df)
~~~

Report:

- slope;
- 95% CI;
- p-value if relevant;
- R² if useful;
- extrapolation caveat.

Do not rely on:

~~~r
last_value - first_value
~~~

unless the question explicitly asks for endpoint difference.

### Rule 7 — Probability wording may mean Bayes, not a test

If the prompt gives:

- prevalence;
- sensitivity;
- specificity;
- false positive rate;
- conditional probability;
- repeated diagnostic test;
- prior / posterior;

then use Bayes’ theorem or a probability table.

Critical distinction:

~~~text
P(Disease | Positive) ≠ P(Positive | Disease)
~~~

Do not force a p-value into a probability-calculation question.

### Rule 8 — Cleaning clues reveal design

Use data structure to infer method:

| Data clue | Likely meaning |
|---|---|
| numeric dose with few levels | ordered factor or dose-response decision |
| character treatment column | grouping factor |
| before/after column | paired design |
| subject ID | possible repeated / paired unit |
| NA with “Died” / dropout comment | meaningful missingness |
| date column | secondary variable unless trend is asked |
| expected ratio in text | goodness-of-fit |
| one row per click/vote/press | possible non-independence |

---

## 6. Quick method map

| Question pattern | Recommended route | Avoid |
|---|---|---|
| continuous outcome, 2 independent groups | t-test / linear model | paired test unless paired |
| continuous outcome, ≥3 groups | one-way ANOVA | repeated t-tests |
| continuous outcome, 2 factors | two-way ANOVA with interaction | ignoring interaction |
| before-after same subject | paired analysis / difference score | independent groups |
| observed counts vs expected ratio | χ² goodness-of-fit | χ² independence |
| two categorical variables, independent rows | χ² independence | using if repeated responses |
| sparse categorical table | Fisher / exact / simulated χ² | blind χ² |
| non-independent proportions | bootstrap / simulation | χ²/Fisher as primary |
| continuous x and y, association | correlation or regression | choosing without target |
| change over time | regression slope + CI | endpoint-only difference |
| binary outcome with predictors | logistic regression | ordinary linear regression |
| count outcome with predictors | Poisson / NB-style route | assuming normality |
| sensitivity/specificity/prevalence | Bayes theorem | confusing conditioning |
| unknown sampling distribution | bootstrap / simulation | overclaiming parametric test |
| non-normal distribution + small sample | bootstrap / simulation | pretending t-test / ANOVA assumptions are met |
| very small population/sample with no valid distributional assumption | bootstrap / exact-style simulation | large-sample approximation |
| lack of independence or overlapping categories | bootstrap / simulation if resampling unit is defensible | ordinary χ² / Fisher / prop.test with hidden independence assumption |
| clustering requested | scaled clustering/PCA | causal claims |

---

## 7. Minimum EDA and assumption route

Recommend only the minimum needed for the chosen method.

| Method | Minimum EDA | Minimum assumption/risk check |
|---|---|---|
| t-test / ANOVA | boxplot + raw points | independence, residual normality, variance, group size |
| two-way ANOVA | grouped/faceted boxplot or interaction plot | interaction, residual plots, balance |
| χ² GoF | observed vs expected table/bar plot | expected counts, mutually exclusive cells |
| χ² independence | proportion bar plot | independence, expected counts |
| bootstrap | bootstrap distribution / CI plot | correct resampling unit |
| bootstrap fallback | bootstrap distribution + sensitivity to resampling unit | whether resampling preserves dependence / overlap structure |
| regression | scatter + fitted line | linearity, residuals, influence, extrapolation |
| Bayes | probability table/tree | conditioning direction |
| clustering | PCA/pair plot | scaling, distance, cluster stability |

---

## 8. Final self-audit before output

Before answering, check:

- Did I identify response, predictors, and independent unit?
- Did I classify the target task correctly?
- Did I detect paired/repeated/non-independent design?
- Did I distinguish χ² goodness-of-fit from χ² independence?
- Did I identify the full expected structure before collapsing categories?
- Did I distinguish correlation, regression, and GLM routes?
- Did I flag Bayesian/probability questions separately from hypothesis tests?
- Did I avoid endpoint-only trend estimation?
- Did I state what method to avoid and why?
- Did I avoid adding unsupported assumptions just to make a familiar method usable?
- If ordinary tests are blocked, did I consider bootstrap / simulation and define the resampling unit?
- Did I keep the output as triage, not a full Rmd answer?
