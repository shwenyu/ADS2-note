---
name: ads2-coding-challenge-rmd
description: Generate exam-ready R Markdown answers for ADS2 open-book coding challenges. Use when the user provides ADS2 exam questions, datasets, variable descriptions, mock tasks, or asks for Rmd/statistical analysis following Dmytro Shytikov's ADS2 marking expectations.
---

# ADS2 Coding Challenge Rmd Skill

## 0. Skill purpose

This Skill converts an ADS2 coding-challenge question into a statistically valid, reproducible, marking-guide-aligned R Markdown answer.

It is not a study summary. It is an execution contract for producing Rmd-ready analysis.

Primary goals:

- choose the correct statistical method;
- justify the method explicitly;
- check assumptions correctly;
- generate reproducible R code;
- write concise but meaningful interpretation;
- avoid invalid formal tests;
- produce a clean R Markdown structure that can knit under exam conditions.

---

## 1. When to use this Skill

Use this Skill when the task involves any of the following:

- ADS2 coding challenge preparation;
- Applied Data Science 2 mock exam answers;
- R Markdown answer generation;
- statistical test selection in R;
- data import, cleaning, plotting, testing, and discussion;
- ANOVA, chi-square, bootstrap, regression, correlation, clustering, or Bayesian probability;
- exam-style answers following Dmytro Shytikov's marking logic.

Typical trigger phrases:

- "帮我写 ADS2 Rmd 答案"
- "生成 coding challenge 答案"
- "按 ADS2 标准分析这个数据"
- "帮我判断用什么 test"
- "把这题写成 R Markdown"
- "根据 Dmytro 的 marking guide 写"

---

## 2. When NOT to use this Skill

Do not use this Skill when:

- the task is general biology/statistics explanation without Rmd output;
- the user only asks for conceptual revision notes;
- the task is software engineering, Java, SQL, physiology, or non-ADS2 coursework;
- the user asks for a polished essay instead of an analysis workflow;
- no statistical/data-analysis task is present;
- the request requires packages, tools, or external APIs unavailable in the exam environment.

If the task is ambiguous, ask one targeted clarification question or state the assumption before proceeding.

---

## 3. Input contract

Before writing the answer, identify the following for each question.

### 3.1 Required extraction

For every question, extract:

1. response variable;
2. explanatory variable(s);
3. variable type:
   - continuous;
   - categorical;
   - count/frequency;
   - paired/repeated;
   - time/order variable;
   - probability/conditional probability;
4. study design:
   - independent groups;
   - paired/repeated observations;
   - overlapping samples;
   - theoretical expected proportions;
   - before/after design;
   - dose-response or time trend;
5. target task:
   - group comparison;
   - association;
   - goodness-of-fit;
   - trend estimation;
   - prediction;
   - bootstrap inference;
   - Bayesian update;
   - clustering / unsupervised exploration;
6. possible data issues:
   - missing values;
   - duplicate rows;
   - wrong column types;
   - unnecessary index columns;
   - wide/long format mismatch;
   - sparse categories;
   - outliers;
   - non-independence;
   - extrapolation risk.

### 3.2 If information is missing

If dataset columns or file type are unknown:

- state what is unknown;
- write robust import/inspection code;
- avoid making unsupported claims;
- choose a defensible default workflow.

---

## 4. Output contract

Return chunked Rmd-ready content.

Use this exact format:

▼ chunk 1 · YAML
~~~md
---
title: "Meaningful title with question and outcome"
author: "Anonymous Marker ID"
date: "`r Sys.Date()`"
output:
  pdf_document:
    toc: true
    toc_depth: 2
    keep_tex: true
  html_document:
    toc: true
---
~~~

▼ chunk 2 · setup
~~~r
...
~~~

Rules:

1. Markdown text goes in separate `md` blocks.
2. R code goes in separate `r` blocks.
3. Do not mix markdown and R in the same block.
4. Do not include R chunk headers such as `{r q1-import}`.
5. The user will manually wrap each R block in Rmd chunk headers.
6. Use short chunk purposes:
   - setup;
   - method overview;
   - import and inspect;
   - clean and reshape;
   - EDA plot;
   - hypotheses;
   - assumption check;
   - main analysis;
   - post-hoc and effect size;
   - interpretation;
   - discussion.

---

## 5. Global answer workflow

Every statistical question must follow this order.

### Step 1. Import and inspect

Always include:

- data import;
- `head()`;
- `str()`;
- `anyNA()`;
- `anyDuplicated()`.

Import must handle possible file formats.

Use this decision rule:

- `.csv` → `read.csv()`;
- `.tsv` / tab-delimited `.txt` → `read.delim()` or `read.table(sep = "\t", header = TRUE)`;
- whitespace-delimited `.txt` → `read.table(header = TRUE)`;
- `.xlsx` / `.xls` → use `readxl::read_excel()` only if available; otherwise state that Excel should be exported to CSV/TSV for exam robustness.

Recommended import helper:

~~~r
import_data <- function(path) {
	ext <- tolower(tools::file_ext(path))

	if (ext == "csv") {
		read.csv(path)
	} else if (ext %in% c("tsv", "tab")) {
		read.delim(path)
	} else if (ext == "txt") {
		read.table(path, header = TRUE)
	} else if (ext %in% c("xlsx", "xls")) {
		if (requireNamespace("readxl", quietly = TRUE)) {
			readxl::read_excel(path)
		} else {
			stop("Excel file detected. Export it as CSV/TSV or install readxl.")
		}
	} else {
		stop("Unsupported file type. Use CSV, TSV, TXT, or Excel.")
	}
}
~~~

When writing the final Rmd, adapt this helper only if needed. Do not over-engineer simple CSV-only tasks.

### Step 2. Clean and reshape

State both:

- what was changed;
- why it was changed.

Common cleaning actions:

- remove index columns such as `X`, `ID`, `...1` if they are not variables;
- convert categorical variables to `factor`;
- convert ordered doses/time groups to ordered `factor`;
- convert character-coded missing values such as `""`, `"NA"`, `"N/A"` to `NA`;
- inspect missingness before dropping rows;
- reshape paired long data using `pivot_wider()`;
- reshape wide repeated-measure data using `pivot_longer()`;
- relocate variables for readability.

Never silently drop rows, columns, missing values, or outliers.

If missingness is biologically meaningful, e.g. `Comment = "Died"`, discuss it as part of interpretation.

### Step 3. Exploratory plot

Choose plot by data structure:

| Data structure | Required plot |
|---|---|
| categorical predictor + continuous response | boxplot + jitter |
| two categorical predictors + continuous response | grouped or faceted boxplot + jitter |
| paired before/after data | paired line plot or difference plot |
| categorical counts | grouped/stacked bar chart + percentages |
| continuous predictor + continuous response | scatter plot + linear fit with CI |
| bootstrap inference | bootstrap distribution or CI plot |
| clustering | pair plot / PCA-style projection / cluster plot if suitable |

Every plot must include:

- title;
- x-axis label;
- y-axis label;
- units if known;
- legend title if legend is used;
- readable theme, normally `theme_minimal(base_size = 11)`;
- raw/primary points whenever possible.

### Step 4. State hypotheses

Always write explicit hypotheses, even if the prompt does not request them.

Include:

- `H0`;
- `H1`;
- `alpha = 0.05`;
- one-sided or two-sided direction.

Do not write vague hypotheses such as "the treatment is useful".

Use symmetric statistical hypotheses:

- "group means are equal" vs "at least one group mean differs";
- "data follow expected proportions" vs "data deviate from expected proportions";
- "slope equals 0" vs "slope differs from 0".

### Step 5. Choose and justify method

Write a short method overview before code:

1. data structure;
2. candidate methods;
3. chosen method and reason;
4. why major alternatives are not chosen.

If assumptions are seriously violated, do not force an invalid formal test.

Preferred fallback options:

- bootstrap confidence interval;
- Wilcoxon / Mann-Whitney;
- Kruskal-Wallis;
- descriptive analysis with CI;
- permutation-style reasoning if appropriate.

Do not write "only for reference" after running an invalid test. Incorrect formal testing is misleading.

### Step 6. Check assumptions

Use one coherent diagnostic strategy.

Do not mix many unrelated diagnostic methods unless clearly justified.

Assumption sets:

- t-test / ANOVA:
  - independence;
  - approximate normality of residuals or group distributions;
  - homogeneity of variance;
  - sufficient group size / balanced design if relevant.
- chi-square:
  - categorical mutually exclusive cells;
  - independent observations;
  - expected counts mostly ≥ 5.
- regression:
  - independence;
  - linearity;
  - homoscedasticity;
  - approximate normality of residuals;
  - influential observations.
- bootstrap:
  - resampling unit correctly defined;
  - independence within resampling units;
  - paired structure preserved if paired.
- Bayesian probability:
  - conditional probabilities correctly defined;
  - sensitivity/specificity/prevalence not confused.

State for each assumption:

- Fits;
- Violates;
- Unclear but acceptable because...

### Step 7. Run main analysis

Report:

- statistic;
- degrees of freedom when applicable;
- p-value;
- estimate;
- 95% confidence interval where possible;
- effect size where applicable.

Do not report only p-values.

### Step 8. Follow-up analysis

Required follow-ups:

- ANOVA significant → `TukeyHSD()` or appropriate post-hoc comparison.
- chi-square significant → inspect expected counts and standardized residuals.
- regression → report slope, confidence interval, R², and influential points.
- bootstrap → report bootstrap CI for estimate or difference.
- paired data → analyze within-subject differences or preserve pairing.
- trend/change over time → use `lm()` slope + 95% CI, not endpoint difference.

### Step 9. Interpret

Each result must include a plain-language sentence.

Use the rule:

- statistic answers "was there evidence?";
- effect size answers "how large?";
- confidence interval answers "how uncertain?";
- biological/practical interpretation answers "why does it matter?"

### Step 10. Discuss and propose next step

Each question must end with a discussion containing:

1. main finding;
2. biological/clinical/practical meaning;
3. mechanism or plausible explanation;
4. at least two limitations or confounders;
5. concrete next experiment or analysis;
6. power analysis if sample size increase is proposed.

Never write only:

- "increase sample size";
- "collect more data";
- "do more experiments".

If recommending larger sample size:

- first calculate current power;
- then estimate required `n` for power ≥ 0.8;
- explain what lower p-value would and would not solve.

---

## 6. Method-selection rules

### 6.1 Continuous response + categorical predictor

Use this when outcome is numeric and predictor is group/treatment/category.

Decision path:

- two independent groups:
  - if assumptions acceptable → two-sample t-test;
  - if assumptions poor → Mann-Whitney or bootstrap CI.
- two paired groups:
  - analyze within-subject difference;
  - if assumptions acceptable → paired t-test;
  - otherwise → Wilcoxon signed-rank or paired bootstrap.
- three or more groups:
  - if one categorical predictor → one-way ANOVA;
  - if two categorical predictors → two-way ANOVA with interaction;
  - if assumptions poor → Kruskal-Wallis or bootstrap, with justification.
- paired before/after with treatment groups:
  - calculate `Difference = after - before`;
  - analyze `Difference ~ Treatment`;
  - do not default to mixed-effects unless explicitly required.

### 6.2 Categorical counts

Use this when data are counts or categories.

Decision path:

- observed counts vs theoretical proportions → chi-square goodness-of-fit;
- two categorical variables in a contingency table → chi-square independence;
- small expected counts → Fisher or simulated chi-square if appropriate;
- repeated/overlapping/non-mutually exclusive responses → do not use chi-square or Fisher; use bootstrap or descriptive comparison.

### 6.3 Non-independent proportions

Red flags:

- same person can vote/respond multiple times;
- early and late groups may contain the same individuals;
- categories are not mutually exclusive;
- repeated button presses or repeated observations.

Required action:

- reject chi-square/Fisher;
- bootstrap proportions or difference in proportions;
- report 95% CI;
- explain why resampling is better aligned with the data-generating process.

### 6.4 Continuous predictor + continuous response

Use regression/correlation.

Required:

- scatter plot with `geom_smooth(method = "lm", se = TRUE)`;
- linear model using `lm(response ~ predictor)`;
- slope estimate + 95% CI;
- R²;
- residual diagnostics;
- Cook's distance or influential point check.

Trend/change rule:

- quantify trend using `lm()` slope;
- never rely only on endpoint difference.

### 6.5 Bayesian probability

Use when given prevalence, sensitivity, specificity, conditional probabilities, diagnostic tests, or repeated tests.

Required:

- define events;
- build a 2 × 2 probability table if possible;
- compute posterior using Bayes' theorem;
- distinguish:
  - sensitivity;
  - specificity;
  - PPV;
  - NPV;
- for repeated tests, use posterior after test 1 as prior for test 2.

### 6.6 Clustering / unsupervised analysis

Use only if question explicitly asks for clustering or unsupervised grouping.

Required:

- scale numeric variables unless scale has meaningful units;
- justify distance metric;
- choose simple method, e.g. k-means or hierarchical clustering;
- visualize clusters;
- avoid overclaiming biological meaning;
- discuss cluster stability and follow-up validation.

---

## 7. Package policy

Exam-safe packages mentioned in teacher material:

- base R;
- tidyverse;
- rmarkdown;
- knitr;
- tinytex;
- pwr.

Allowed if available and useful:

- ggplot2, dplyr, tidyr, readr as part of tidyverse;
- effectsize for effect sizes;
- car for Levene's test;
- readxl for Excel import only if available.

Avoid obscure packages.

If a non-core package is used, provide a base-R or simple fallback where possible.

Setup chunk should usually include:

~~~r
knitr::opts_chunk$set(
	echo = TRUE,
	message = FALSE,
	warning = FALSE,
	tidy = TRUE,
	tidy.opts = list(width.cutoff = 60),
	fig.width = 7,
	fig.height = 4.5
)

library(tidyverse)
library(pwr)

alpha <- 0.05
B <- 10000
set.seed(123)
~~~

Load `effectsize`, `car`, or `readxl` only if actually used.

---

## 8. Evidence-binding rule

Every written claim must be supported by nearby code output.

Do not write:

- "we calculated";
- "we fitted";
- "we compared";
- "there was a trend";
- "assumptions were checked";
- "the data were cleaned";
- "there was an increase";

unless the relevant code appears in the preceding or nearby R chunk.

Conversely, do not run code without interpreting it.

Rule:

- If it is written, it must be evidenced.
- If it is computed, it must be interpreted.

---

## 9. R Markdown and knitting rules

Keep the document simple.

Required formatting:

- blank line between headings, paragraphs, and code chunks;
- heading syntax must include a space: `# Header`, not `#Header`;
- avoid unusual LaTeX;
- avoid special Unicode if knitting to PDF becomes unstable;
- keep code lines short;
- use simple Markdown tables only when useful.

If PDF fails:

1. read the error message;
2. check whether the problem is R code or LaTeX;
3. try HTML or Word output;
4. set `keep_md: true` or `keep_tex: true` if debugging;
5. cut the document into chunks to locate the error;
6. do not spend exam time on `tlmgr` or complex LaTeX fixes;
7. submit Rmd/R/HTML/Word if PDF cannot be produced.

---

## 10. Statistical reporting templates

### 10.1 ANOVA

Use:

A [one-way/two-way] ANOVA tested whether [response] differed by [factor(s)]. The result showed [significant/no significant] evidence for [effect], F(df1, df2) = ..., p = ..., eta-squared/partial eta-squared = ..., 95% CI [..., ...]. This means that ...

If significant:

Tukey HSD post-hoc comparisons showed that ... Therefore, ...

### 10.2 t-test

Use:

A [paired/independent] t-test showed that [group A] and [group B] [did/did not] differ in [response], t(df) = ..., p = ..., mean difference = ..., 95% CI [..., ...], Cohen's d = .... This means that ...

### 10.3 Chi-square

Use:

A chi-square [goodness-of-fit/test of independence] showed that ..., chi-square(df) = ..., p = ..., Cramer's V = .... This means that ...

Then add:

The expected counts were ..., and the largest standardized residuals were ..., indicating that these cells contributed most to the deviation.

### 10.4 Regression

Use:

Linear regression showed that [response] changed by ... units per one-unit increase in [predictor], beta = ..., 95% CI [..., ...], t(df) = ..., p = ..., R² = .... This means that ...

Mention extrapolation if prediction is outside observed predictor range.

### 10.5 Bootstrap

Use:

Because the independence/parametric assumptions were not satisfied, a bootstrap approach was used. The bootstrap 95% CI for [estimate/difference] was [..., ...]. Because the interval [does/does not] include [null value], ...

### 10.6 Bayesian inference

Use:

Using Bayes' theorem, the posterior probability of [event] given [evidence] is .... This means that ...

Then interpret PPV/NPV, sensitivity, and specificity separately.

---

## 11. Anti-patterns

Avoid:

1. producing a conceptual summary instead of an Rmd answer;
2. skipping `head()`, `str()`, `anyNA()`, or `anyDuplicated()`;
3. using CSV-only import code when file type is unknown;
4. dropping missing values without checking their pattern;
5. deleting outliers without evidence;
6. using chi-square with non-independent observations;
7. using t-test/ANOVA despite clear assumption failure;
8. writing "only for reference" after an invalid test;
9. quantifying trends using endpoint difference only;
10. reporting p-value only;
11. omitting effect size and confidence interval;
12. omitting post-hoc after significant ANOVA;
13. omitting residuals after significant chi-square;
14. giving "increase sample size" without power analysis;
15. using excessive theory not needed for the answer;
16. using too many package dependencies;
17. mixing inconsistent terminology;
18. overcomplicating Markdown/LaTeX during the exam.

---

## 12. Minimal final self-audit

Before output, check:

- Did every question follow import → clean → plot → hypotheses → assumptions → analysis → effect size/CI → interpretation → discussion?
- Did method choice match variable types and study design?
- Did I avoid invalid formal tests?
- Did I include code evidence for every written claim?
- Did I interpret every computed result?
- Did every plot have title, axis labels, units where known, and legend title?
- Did every statistical result include statistic, df where relevant, p-value, estimate, CI, and effect size where possible?
- Did discussion include mechanism, confounders/limitations, and next step?
- Did sample-size discussion include power analysis?
- Did output use separated `md` and `r` blocks?

Only output after this audit passes.

---

# Distilled Reference Pack

This section is reference material. Use it after applying the decision rules above. Do not blindly copy it as an answer.

---

## R1. Teacher's marking anchors

ADS2 coding challenge structure:

- 3 problems, 25% each;
- R Markdown documentation, 25%;
- 3 hours total;
- open-book, no restrictions.

Teacher expectations:

- import and clean data;
- plot in a way that addresses the question;
- choose an appropriate statistical test;
- explain the choice explicitly;
- formulate correct hypotheses;
- check assumptions;
- discuss improvement/follow-up creatively;
- do not ask for a bigger sample size unless power analysis supports it.

Available/basic package expectation:

- base R;
- tidyverse;
- rmarkdown;
- knitr;
- tinytex;
- pwr.

---

## R2. Universal 8-step ADS2 pipeline

For each question:

1. Import and inspect.
2. Clean and reshape.
3. EDA plot.
4. State H0/H1.
5. Choose and justify test.
6. Check assumptions.
7. Run main test.
8. Post-hoc/effect size/CI, then discuss and propose next step.

---

## R3. Plot selection reference

| Variables | Recommended plot |
|---|---|
| categorical independent + continuous response, unpaired | boxplot, whisker plot, strip chart, jitter |
| categorical independent + continuous response, paired | paired points/lines, histogram or boxplot of difference |
| continuous independent + continuous response | scatter plot + trend line |
| categorical data | bar chart, table with percentages, pie chart only if simple |

Prefer plots with raw primary points.

---

## R4. ANOVA prototype: ToothGrowth / teeth

Trigger:

- continuous response;
- one or two categorical predictors;
- group comparison;
- factorial design.

Key choices:

- one factor → one-way ANOVA;
- two factors → two-way ANOVA with interaction;
- significant ANOVA → Tukey HSD;
- report eta-squared or partial eta-squared;
- check normality/residuals and variance assumptions.

Typical cleaning:

- convert `dose` to ordered factor;
- convert `supp` to factor;
- remove unnecessary index column;
- use grouped boxplot + jitter.

Interpretation focus:

- main effects;
- interaction;
- which pairs differ;
- whether high-dose groups become indistinguishable.

---

## R5. Paired treatment prototype: t1d_drug

Trigger:

- same subject measured before and after;
- treatment groups;
- missing after value may indicate death or dropout.

Required logic:

- inspect missingness before dropping;
- reshape long to wide;
- calculate `Difference = after - before`;
- analyze `Difference ~ Treatment`;
- discuss toxicity if death/missingness clusters in treatment groups.

Power logic:

- calculate current power if non-significant but effect appears meaningful;
- estimate required `n` for power ≥ 0.8.

---

## R6. Chi-square goodness-of-fit prototype: genotype

Trigger:

- observed counts vs theoretical proportions;
- Mendelian ratios such as 25:50:25;
- categorical frequency data.

Required:

- build count table;
- define expected probabilities;
- check expected counts;
- run chi-square goodness-of-fit;
- inspect residuals;
- report Cramer's V;
- discuss biological mechanism such as lethality, penetrance, sex effect, or selection.

---

## R7. Bootstrap prototype: coffee shop non-independence

Trigger:

- proportions look like a 2 × 2 table;
- but observations are not independent;
- same individuals may contribute multiple observations;
- groups overlap across time periods.

Required:

- explicitly reject chi-square/Fisher;
- bootstrap group proportions or difference;
- use 10,000 resamples unless exam time requires fewer;
- report 95% CI;
- conclude based on whether difference CI includes 0.

Key phrase:

Because observations are not independent, chi-square and Fisher's exact test are not appropriate. A bootstrap comparison better matches the uncertainty in the observed proportions.

---

## R8. Regression prototype: cars

Trigger:

- continuous response and continuous predictor;
- relationship, prediction, trend, slope, change over time.

Required:

- unit conversion if needed;
- scatter plot + linear fit;
- `lm(response ~ predictor)`;
- residual diagnostics;
- Cook's distance;
- slope + 95% CI;
- R²;
- prediction interval if predicting;
- extrapolation warning if new value lies outside observed range.

Critical rule:

Use linear model slope to quantify trend. Do not use endpoint difference alone.

---

## R9. Bayesian probability prototype: IVF / diagnostic test

Trigger:

- prevalence;
- sensitivity;
- specificity;
- conditional probability;
- repeated test.

Required:

- define prior;
- calculate likelihood;
- calculate evidence;
- calculate posterior;
- distinguish PPV from sensitivity and NPV from specificity;
- for repeated positive tests, use posterior 1 as prior 2.

Interpretation focus:

- high sensitivity does not guarantee high PPV when prevalence is low;
- negative results may be more reliable than positive results if specificity is limited.

---

## R10. Discussion upgrade formula

Weak:

The treatment worked. More data should be collected.

Strong:

The data suggest [main result]. This may occur because [mechanism 1] or [mechanism 2]. However, [confounder 1] and [confounder 2] could also contribute. A follow-up study should [specific design], control for [specific covariate], and use power analysis to determine whether additional replicates are necessary.

Required discussion dimensions:

- mechanism;
- confounders;
- limitation;
- subgroup or covariate analysis if relevant;
- concrete next experiment;
- power-backed sample size only when justified.

---

## R11. Exam-safe Rmd structure

Use a simple structure:

- YAML;
- setup;
- question heading;
- method overview;
- import and inspect;
- cleaning;
- plot;
- hypotheses;
- assumptions;
- main test;
- follow-up;
- interpretation;
- discussion.

Keep Markdown simple. If PDF fails, produce HTML or Word rather than spending exam time on LaTeX.
