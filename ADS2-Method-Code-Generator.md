---
name: ads2-code-reference-bank
description: A reference bank for ADS2 exam-ready R/Rmd code snippets. Use when the user asks for a specific part of an analysis workflow, such as import, inspection, cleaning, plotting, ANOVA, chi-square, bootstrap, regression, GLM, Bayesian probability, assumptions, post-hoc tests, effect size, or interpretation code. The user may provide dataframe names, variable names, file names, expected proportions, or context; return only the relevant code section unless they ask for a full workflow.
---

# ADS2 Skill 2 — Code Reference Bank

## 0. Purpose

This Skill is a **code reference bank**, not a full-answer generator by default.

When the user asks for a specific code component, return the matching ADS2-ready R/Rmd code snippet using the user's provided information.

Examples of valid user requests:

- "给我 import + inspect 的代码"
- "这题需要 two-way ANOVA，df 叫 tooth，response 是 len，predictors 是 supp 和 dose"
- "给我 chi-square goodness-of-fit 的代码，比例是 1:2:1"
- "我需要 bootstrap proportion difference 这一段"
- "给我 regression plot + lm + CI"
- "帮我写 assumption check"
- "我只要 TukeyHSD 和 effect size"
- "把这部分改成我的变量名"

Default behavior:

1. Identify which code block the user needs.
2. Substitute the user's dataframe, variable names, file names, labels, expected probabilities, and units.
3. Return only the relevant code section.
4. Add a short Chinese explanation only if needed.
5. Do not generate a full Rmd answer unless explicitly asked.

---

## 1. How to respond

### 1.1 If the user asks for one code part

Return:

1. a short heading;
2. one R code block;
3. optional 1–3 bullet notes explaining what to replace or check.

Example format:

### Import + inspection code

    df <- read.csv("file.csv")

    head(df)
    str(df)
    summary(df)

    anyNA(df)
    colSums(is.na(df))
    anyDuplicated(df)

Notes:

- Replace `"file.csv"` with the actual file name.
- Use this before cleaning or modelling.

### 1.2 If the user asks for several code parts

Return the requested parts in order.

Example:

1. Import + inspection
2. Cleaning
3. Plot
4. Main test
5. Interpretation template

### 1.3 If the user gives a method but lacks variables

Ask only for the missing fields needed to write correct code.

Minimum information by task:

| Task | Required information |
|---|---|
| Import | file name, dataframe name |
| Cleaning | dataframe name, variable names, desired type |
| Plot | dataframe, x, y, group/fill if needed |
| t-test | dataframe, response, two-level group |
| One-way ANOVA | dataframe, response, group |
| Two-way ANOVA | dataframe, response, factor1, factor2 |
| Paired design | ID, before/after variable, response, treatment if present |
| Chi-square GOF | observed category/counts, expected proportions |
| Chi-square independence | two categorical variables |
| Bootstrap proportions | success/failure counts or binary vector |
| Linear regression | response, predictor |
| Logistic regression | binary response, predictor(s), event coding |
| Poisson regression | count response, predictor(s) |
| Bayesian probability | prior/prevalence, sensitivity, specificity |
| Clustering | numeric variables, chosen k if known |

### 1.4 If the requested method is clearly invalid

Briefly flag the issue and give the safer code branch.

Example:

"这里不建议用 chi-square，因为同一个人可能重复贡献记录，独立性 (Independence) 破坏。更合适的是 bootstrap difference in proportions。下面给对应代码。"

---

## 2. Style rules

Do:

1. Use the user's exact object and variable names.
2. Keep code short and exam-ready.
3. Use base R + tidyverse as default.
4. Use optional packages only when needed.
5. Prefer clear object names:
   - `df`
   - `model`
   - `anova_model`
   - `chi`
   - `boot_diff`
   - `result_table`
6. Include `set.seed(123)` for simulation, bootstrap, and k-means.
7. Add comments only when they clarify a step.
8. Keep each code block knit-safe.

Do not:

1. Provide the entire reference bank unless the user asks.
2. Re-explain the whole method-selection logic.
3. Invent numeric results.
4. Use placeholder names when user already gave names.
5. Mix unrelated code branches.
6. Silently drop missing data.
7. Escape R syntax such as `$`, `<-`, `%>%`, `~`, or `*` inside code.
8. Let `is.na`, `as.data.frame`, or `as.Date` become Markdown links.
9. Use chi-square when observations are repeated/non-independent.
10. Use endpoint difference alone for time trend.

---

## 3. Universal code blocks

### 3.1 Global setup

Use when the user asks for setup, full workflow, or Rmd start.

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

    library(tidyverse)

    alpha <- 0.05
    B <- 10000
    set.seed(123)

Optional packages:

    library(effectsize)
    library(car)
    library(pwr)

---

### 3.2 Import + inspect

Use when the user asks for data loading or initial checks.

    df <- read.csv("file.csv")

    head(df)
    str(df)
    summary(df)

    anyNA(df)
    colSums(is.na(df))
    anyDuplicated(df)

If dataframe already exists:

    head(df)
    str(df)
    summary(df)

    anyNA(df)
    colSums(is.na(df))
    anyDuplicated(df)

---

### 3.3 Missing-value check by group

Use before group comparison or paired analysis.

    df %>%
      group_by(group) %>%
      summarise(
        n = n(),
        missing_response = sum(is.na(response)),
        .groups = "drop"
      )

For two factors:

    df %>%
      group_by(factor1, factor2) %>%
      summarise(
        n = n(),
        missing_response = sum(is.na(response)),
        .groups = "drop"
      )

---

### 3.4 Factor conversion

Use for categorical predictors.

    df <- df %>%
      mutate(
        group = factor(group)
      )

For two categorical predictors:

    df <- df %>%
      mutate(
        factor1 = factor(factor1),
        factor2 = factor(factor2)
      )

For ordered dose:

    df <- df %>%
      mutate(
        dose = factor(
          dose,
          levels = c(0.5, 1, 2),
          ordered = TRUE
        )
      )

---

### 3.5 Basic summary table

Use before plotting or testing.

    df %>%
      group_by(group) %>%
      summarise(
        n = n(),
        mean = mean(response, na.rm = TRUE),
        sd = sd(response, na.rm = TRUE),
        median = median(response, na.rm = TRUE),
        iqr = IQR(response, na.rm = TRUE),
        .groups = "drop"
      )

For two factors:

    df %>%
      group_by(factor1, factor2) %>%
      summarise(
        n = n(),
        mean = mean(response, na.rm = TRUE),
        sd = sd(response, na.rm = TRUE),
        .groups = "drop"
      )

---

## 4. Plot reference bank

### 4.1 Continuous response by one group

    ggplot(df, aes(x = group, y = response, fill = group)) +
      geom_boxplot(alpha = 0.6, outlier.shape = NA) +
      geom_jitter(width = 0.15, alpha = 0.6, size = 1.2) +
      labs(
        title = "Response by group",
        x = "Group",
        y = "Response, units",
        fill = "Group"
      ) +
      theme_minimal(base_size = 11) +
      theme(legend.position = "none")

---

### 4.2 Continuous response by two factors

    ggplot(df, aes(x = factor2, y = response, fill = factor1)) +
      geom_boxplot(alpha = 0.6, outlier.shape = NA) +
      geom_jitter(
        aes(color = factor1),
        position = position_jitterdodge(jitter.width = 0.15),
        alpha = 0.6,
        size = 1.2
      ) +
      labs(
        title = "Response by two factors",
        x = "Factor 2",
        y = "Response, units",
        fill = "Factor 1",
        color = "Factor 1"
      ) +
      theme_minimal(base_size = 11)

Optional interaction plot:

    interaction.plot(
      x.factor = df$factor2,
      trace.factor = df$factor1,
      response = df$response,
      xlab = "Factor 2",
      ylab = "Mean response",
      trace.label = "Factor 1"
    )

---

### 4.3 Paired before-after line plot

    ggplot(df, aes(x = Measurement, y = Value, group = ID)) +
      geom_line(alpha = 0.4) +
      geom_point(aes(color = Treatment), size = 1.5) +
      facet_wrap(~ Treatment) +
      labs(
        title = "Before-after measurements by treatment",
        x = "Measurement time",
        y = "Response, units",
        color = "Treatment"
      ) +
      theme_minimal(base_size = 11)

---

### 4.4 Difference score plot

    ggplot(df_analysis, aes(x = Treatment, y = Difference, fill = Treatment)) +
      geom_boxplot(alpha = 0.6, outlier.shape = NA) +
      geom_jitter(width = 0.15, alpha = 0.6, size = 1.2) +
      labs(
        title = "Within-subject change by treatment",
        x = "Treatment",
        y = "After-before difference, units",
        fill = "Treatment"
      ) +
      theme_minimal(base_size = 11)

---

### 4.5 Observed vs expected count plot

    result_table %>%
      pivot_longer(
        cols = c(observed, expected),
        names_to = "count_type",
        values_to = "count"
      ) %>%
      ggplot(aes(x = category, y = count, fill = count_type)) +
      geom_col(position = "dodge") +
      labs(
        title = "Observed and expected counts",
        x = "Category",
        y = "Count",
        fill = "Count type"
      ) +
      theme_minimal(base_size = 11)

---

### 4.6 Proportion bar plot for two categorical variables

    as.data.frame(tab) %>%
      group_by(Var1) %>%
      mutate(prop = Freq / sum(Freq)) %>%
      ggplot(aes(x = Var1, y = prop, fill = Var2)) +
      geom_col(position = "fill") +
      labs(
        title = "Proportional distribution by group",
        x = "Group",
        y = "Proportion",
        fill = "Category"
      ) +
      theme_minimal(base_size = 11)

---

### 4.7 Scatterplot with linear trend

    ggplot(df, aes(x = predictor, y = response)) +
      geom_point(color = "steelblue", alpha = 0.7) +
      geom_smooth(method = "lm", se = TRUE, color = "firebrick") +
      labs(
        title = "Linear relationship between predictor and response",
        x = "Predictor, units",
        y = "Response, units"
      ) +
      theme_minimal(base_size = 11)

---

### 4.8 Time trend plot

    ggplot(df, aes(x = date, y = response)) +
      geom_line(color = "steelblue") +
      geom_point(alpha = 0.7) +
      geom_smooth(method = "lm", se = TRUE, color = "firebrick") +
      labs(
        title = "Response over time",
        x = "Time",
        y = "Response, units"
      ) +
      theme_minimal(base_size = 11)

---

### 4.9 Bootstrap distribution plot

    tibble(boot_stat = boot_stat) %>%
      ggplot(aes(x = boot_stat)) +
      geom_histogram(
        bins = 40,
        fill = "steelblue",
        color = "white"
      ) +
      labs(
        title = "Bootstrap distribution",
        x = "Bootstrap statistic",
        y = "Count"
      ) +
      theme_minimal(base_size = 11)

---

## 5. t-test reference bank

### 5.1 Independent two-sample t-test

Use for continuous response + two independent groups.

    t_test_result <- t.test(response ~ group, data = df)

    t_test_result

Effect size:

    effectsize::cohens_d(response ~ group, data = df)

Interpretation template:

    The Welch two-sample t-test compares the mean response between two independent groups. Report the estimated mean difference, 95% confidence interval, and p-value.

---

### 5.2 Paired t-test

Use for same subjects measured twice.

    paired_result <- t.test(
      df_analysis$after,
      df_analysis$before,
      paired = TRUE
    )

    paired_result

Interpretation template:

    The paired t-test evaluates whether the mean within-subject change differs from zero.

---

## 6. ANOVA reference bank

### 6.1 One-way ANOVA

Use for continuous response + one categorical predictor with ≥3 groups.

    model <- aov(response ~ group, data = df)

    par(mfrow = c(1, 2))
    plot(model, which = 1)
    plot(model, which = 2)
    par(mfrow = c(1, 1))

    summary(model)

Post-hoc:

    TukeyHSD(model)

Effect size:

    effectsize::eta_squared(model)

Interpretation template:

    The one-way ANOVA tests whether mean response differs among groups. If the ANOVA is significant, use TukeyHSD to identify which pairs differ.

---

### 6.2 Two-way ANOVA with interaction

Use for continuous response + two categorical predictors.

    anova_model <- aov(response ~ factor1 * factor2, data = df)

    par(mfrow = c(1, 2))
    plot(anova_model, which = 1)
    plot(anova_model, which = 2)
    par(mfrow = c(1, 1))

    summary(anova_model)

Post-hoc:

    TukeyHSD(anova_model)

Effect size:

    effectsize::eta_squared(anova_model, partial = TRUE)

Interpretation template:

    Interpret the interaction first. If the interaction is meaningful, the effect of one factor depends on the level of the other factor, so main effects alone are insufficient.

---

### 6.3 ANOVA on paired difference scores

Use for before-after data when comparing change across treatment groups.

    df_wide <- df %>%
      pivot_wider(
        names_from = Measurement,
        values_from = Value
      ) %>%
      mutate(
        Difference = after - before
      )

    df_analysis <- df_wide %>%
      filter(!is.na(Difference))

    diff_model <- aov(Difference ~ Treatment, data = df_analysis)

    par(mfrow = c(1, 2))
    plot(diff_model, which = 1)
    plot(diff_model, which = 2)
    par(mfrow = c(1, 1))

    summary(diff_model)
    TukeyHSD(diff_model)
    effectsize::eta_squared(diff_model)

Interpretation template:

    The response variable is the within-subject change. This avoids incorrectly treating before and after measurements as independent observations.

---

## 7. Chi-square reference bank

### 7.1 Chi-square goodness-of-fit

Use for observed counts vs theoretical proportions.

    observed <- table(df$category)

    expected_p <- c(
      level1 = 0.25,
      level2 = 0.50,
      level3 = 0.25
    )

    sum(expected_p)

    chi <- chisq.test(
      x = as.numeric(observed),
      p = expected_p
    )

    chi
    chi$expected
    chi$residuals

Result table:

    result_table <- tibble(
      category = names(observed),
      observed = as.numeric(observed),
      expected = as.numeric(chi$expected),
      residual = as.numeric(chi$residuals)
    )

    result_table

Effect size:

    cramers_v <- sqrt(
      as.numeric(chi$statistic) /
        (sum(observed) * (length(observed) - 1))
    )

    cramers_v

Interpretation template:

    This goodness-of-fit test checks whether the observed count distribution differs from the theoretical expected distribution.

---

### 7.2 Mendelian 1:2:1 goodness-of-fit

    observed <- table(
      factor(df$genotype, levels = c("WT", "het", "mut"))
    )

    expected_p <- c(
      WT = 0.25,
      het = 0.50,
      mut = 0.25
    )

    chi <- chisq.test(
      x = as.numeric(observed),
      p = expected_p
    )

    chi
    chi$expected
    chi$residuals

---

### 7.3 Sex × genotype expected proportions

Use when expected proportions are sex 50/50 and genotype 1:2:1.

    mice <- table(
      df$sex,
      factor(df$genotype, levels = c("WT", "het", "mut"))
    )

    expected_p <- c(
      0.125, 0.25, 0.125,
      0.125, 0.25, 0.125
    )

    chi <- chisq.test(
      x = as.numeric(mice),
      p = expected_p
    )

    chi

    matrix(
      chi$expected,
      nrow = nrow(mice),
      dimnames = dimnames(mice)
    )

    matrix(
      chi$residuals,
      nrow = nrow(mice),
      dimnames = dimnames(mice)
    )

---

### 7.4 Chi-square test of independence

Use for two independent categorical variables.

    tab <- table(df$var1, df$var2)

    chi <- chisq.test(tab)

    tab
    chi
    chi$expected
    chi$residuals

If expected counts are small:

    fisher.test(tab)

Effect size:

    cramers_v <- sqrt(
      as.numeric(chi$statistic) /
        (sum(tab) * (min(dim(tab)) - 1))
    )

    cramers_v

Interpretation template:

    This test evaluates whether the distribution of one categorical variable differs across the levels of another categorical variable. It requires independent observations.

---

## 8. Bootstrap reference bank

### 8.1 Bootstrap difference in proportions from counts

Use when comparing proportions, especially if chi-square independence is questionable.

    early_satisfied <- 864
    early_unsatisfied <- 714

    late_satisfied <- 980
    late_unsatisfied <- 473

    early_results <- c(
      rep(1, early_satisfied),
      rep(0, early_unsatisfied)
    )

    late_results <- c(
      rep(1, late_satisfied),
      rep(0, late_unsatisfied)
    )

    B <- 10000
    set.seed(123)

    early_boot <- replicate(
      B,
      mean(
        sample(
          early_results,
          length(early_results),
          replace = TRUE
        )
      )
    )

    late_boot <- replicate(
      B,
      mean(
        sample(
          late_results,
          length(late_results),
          replace = TRUE
        )
      )
    )

    diff_boot <- late_boot - early_boot

    quantile(early_boot, c(0.025, 0.975))
    quantile(late_boot, c(0.025, 0.975))
    quantile(diff_boot, c(0.025, 0.975))

Optional bootstrap p-value:

    mean(diff_boot <= 0)

---

### 8.2 Bootstrap confidence interval for mean

    B <- 10000
    set.seed(123)

    boot_mean <- replicate(
      B,
      mean(
        sample(df$response, replace = TRUE),
        na.rm = TRUE
      )
    )

    quantile(boot_mean, c(0.025, 0.975))

---

### 8.3 Bootstrap confidence interval for median

    B <- 10000
    set.seed(123)

    boot_median <- replicate(
      B,
      median(
        sample(df$response, replace = TRUE),
        na.rm = TRUE
      )
    )

    quantile(boot_median, c(0.025, 0.975))

---

### 8.4 Bootstrap difference in means by group

    group_a <- df %>%
      filter(group == "A") %>%
      pull(response)

    group_b <- df %>%
      filter(group == "B") %>%
      pull(response)

    B <- 10000
    set.seed(123)

    boot_diff <- replicate(
      B,
      mean(sample(group_b, replace = TRUE), na.rm = TRUE) -
        mean(sample(group_a, replace = TRUE), na.rm = TRUE)
    )

    quantile(boot_diff, c(0.025, 0.975))

Interpretation template:

    The bootstrap confidence interval estimates uncertainty in the statistic by resampling from the observed data. It does not fix biased sampling or non-independent design unless resampling is done at the correct unit level.

---

## 9. Regression reference bank

### 9.1 Linear regression

Use for continuous response + continuous predictor.

    model <- lm(response ~ predictor, data = df)

    summary(model)
    confint(model)

Diagnostics:

    par(mfrow = c(2, 2))
    plot(model)
    par(mfrow = c(1, 1))

Influence check:

    cooks <- cooks.distance(model)
    plot(cooks, type = "h")

Prediction:

    new_data <- data.frame(
      predictor = c(value1, value2)
    )

    predict(
      model,
      newdata = new_data,
      interval = "prediction"
    )

Interpretation template:

    The slope estimates the expected change in response for a one-unit increase in predictor. Use the slope and 95% confidence interval to quantify trend.

---

### 9.2 Multiple linear regression

Use for continuous response + multiple predictors.

    model <- lm(response ~ predictor1 + predictor2 + predictor3, data = df)

    summary(model)
    confint(model)

Diagnostics:

    par(mfrow = c(2, 2))
    plot(model)
    par(mfrow = c(1, 1))

Interpretation template:

    Each coefficient estimates the association between that predictor and the response while holding the other predictors constant.

---

### 9.3 Regression with interaction

    model_interaction <- lm(
      response ~ predictor1 * predictor2,
      data = df
    )

    summary(model_interaction)
    confint(model_interaction)

Interpretation template:

    The interaction term tests whether the effect of one predictor changes depending on the level/value of the other predictor.

---

### 9.4 Correlation

Pearson correlation:

    cor.test(
      df$xvar,
      df$yvar,
      method = "pearson"
    )

Spearman correlation:

    cor.test(
      df$xvar,
      df$yvar,
      method = "spearman"
    )

Interpretation template:

    Correlation measures the strength and direction of association, but it does not estimate a slope and does not imply causation.

---

## 10. GLM reference bank

### 10.1 Logistic regression

Use for binary response.

    df <- df %>%
      mutate(
        outcome01 = if_else(outcome == "yes", 1, 0)
      )

    logit_model <- glm(
      outcome01 ~ predictor,
      data = df,
      family = binomial
    )

    summary(logit_model)
    confint(logit_model)

Odds ratios:

    odds_ratios <- exp(coef(logit_model))
    odds_ci <- exp(confint(logit_model))

    odds_ratios
    odds_ci

Interpretation template:

    Exponentiated coefficients are odds ratios. An odds ratio above 1 means higher odds of the event; below 1 means lower odds.

---

### 10.2 Logistic regression with multiple predictors

    logit_model <- glm(
      outcome01 ~ predictor1 + predictor2 + predictor3,
      data = df,
      family = binomial
    )

    summary(logit_model)

    exp(coef(logit_model))
    exp(confint(logit_model))

---

### 10.3 Poisson regression for count outcome

    pois_model <- glm(
      count_response ~ predictor,
      data = df,
      family = poisson
    )

    summary(pois_model)
    confint(pois_model)

Rate ratios:

    rate_ratios <- exp(coef(pois_model))
    rate_ci <- exp(confint(pois_model))

    rate_ratios
    rate_ci

Overdispersion check:

    dispersion <- sum(residuals(pois_model, type = "pearson")^2) /
      df.residual(pois_model)

    dispersion

If overdispersed:

    quasi_model <- glm(
      count_response ~ predictor,
      data = df,
      family = quasipoisson
    )

    summary(quasi_model)

Interpretation template:

    Exponentiated coefficients are rate ratios. If overdispersion is much larger than 1, use a quasi-Poisson route or mention overdispersion as a limitation.

---

## 11. Time trend reference bank

### 11.1 Date conversion

    df <- df %>%
      mutate(
        date = as.Date(date)
      )

If time is year:

    df <- df %>%
      mutate(
        year = as.numeric(year)
      )

---

### 11.2 Linear time trend

    trend_model <- lm(response ~ date, data = df)

    summary(trend_model)
    confint(trend_model)

Interpretation template:

    The slope estimates the average change in response per unit time. Do not use endpoint difference alone as evidence for a trend.

---

## 12. Bayesian probability reference bank

### 12.1 Diagnostic test: PPV and NPV

Use for prevalence/prior, sensitivity, specificity.

    prior <- 0.222
    sensitivity <- 0.95
    specificity <- 0.80

    p_test_pos <- sensitivity * prior +
      (1 - specificity) * (1 - prior)

    posterior_pos <- sensitivity * prior / p_test_pos

    p_test_neg <- (1 - sensitivity) * prior +
      specificity * (1 - prior)

    posterior_given_neg <- (1 - sensitivity) * prior / p_test_neg

    posterior_pos
    posterior_given_neg

Interpretation template:

    `posterior_pos` is P(condition | positive test), not P(positive test | condition).

---

### 12.2 Repeated positive test

    prior_2 <- posterior_pos

    p_test_pos_2 <- sensitivity * prior_2 +
      (1 - specificity) * (1 - prior_2)

    posterior_pos_2 <- sensitivity * prior_2 / p_test_pos_2

    posterior_pos_2

---

### 12.3 Probability table

    bayes_table <- tibble(
      status = c("Disease", "No disease"),
      prior = c(prior, 1 - prior),
      test_positive = c(sensitivity, 1 - specificity),
      test_negative = c(1 - sensitivity, specificity)
    ) %>%
      mutate(
        joint_positive = prior * test_positive,
        joint_negative = prior * test_negative
      )

    bayes_table

---

## 13. Clustering reference bank

### 13.1 Select and scale numeric variables

    cluster_data <- df %>%
      select(var1, var2, var3) %>%
      drop_na()

    cluster_scaled <- scale(cluster_data)

---

### 13.2 Elbow plot

    set.seed(123)

    wss <- map_dbl(
      1:10,
      ~ kmeans(cluster_scaled, centers = .x, nstart = 25)$tot.withinss
    )

    tibble(k = 1:10, wss = wss) %>%
      ggplot(aes(x = k, y = wss)) +
      geom_line() +
      geom_point() +
      labs(
        title = "Elbow plot for k-means clustering",
        x = "Number of clusters",
        y = "Total within-cluster sum of squares"
      ) +
      theme_minimal(base_size = 11)

---

### 13.3 K-means clustering

    set.seed(123)

    km <- kmeans(
      cluster_scaled,
      centers = 3,
      nstart = 25
    )

    df_clustered <- cluster_data %>%
      mutate(cluster = factor(km$cluster))

    df_clustered

---

### 13.4 Cluster plot

    ggplot(df_clustered, aes(x = var1, y = var2, color = cluster)) +
      geom_point(alpha = 0.8) +
      labs(
        title = "K-means clusters",
        x = "Variable 1",
        y = "Variable 2",
        color = "Cluster"
      ) +
      theme_minimal(base_size = 11)

Interpretation template:

    Clustering is exploratory. Do not claim that clusters prove biological groups unless there is external validation.

---

## 14. Power and sample size reference bank

### 14.1 One-way ANOVA power

    pwr.anova.test(
      k = number_of_groups,
      f = observed_f,
      sig.level = alpha,
      power = 0.8
    )

---

### 14.2 Two-sample t-test power

    pwr.t.test(
      d = observed_d,
      sig.level = alpha,
      power = 0.8,
      type = "two.sample",
      alternative = "two.sided"
    )

---

### 14.3 Proportion test power

    pwr.2p.test(
      h = effect_size_h,
      sig.level = alpha,
      power = 0.8,
      alternative = "two.sided"
    )

---

## 15. Interpretation template bank

### 15.1 p-value wording

    The p-value represents the probability of observing data at least this extreme if the null hypothesis were true. A small p-value provides evidence against the null hypothesis, but it does not measure effect size or practical importance.

### 15.2 Confidence interval wording

    The 95% confidence interval gives a range of plausible values for the population parameter under repeated sampling. Wider intervals indicate greater uncertainty.

### 15.3 Effect size wording

    Effect size describes the magnitude of the difference or association, which should be interpreted alongside the p-value and confidence interval.

### 15.4 ANOVA wording

    The ANOVA tests whether at least one group mean differs from the others. It does not directly identify which groups differ, so post-hoc comparisons are needed when the overall test is significant.

### 15.5 Interaction wording

    An interaction means the effect of one predictor depends on the level of another predictor. Therefore, main effects should be interpreted cautiously.

### 15.6 Regression wording

    The slope estimates the expected change in the response for a one-unit increase in the predictor. This should not be interpreted causally unless the study design supports causal inference.

### 15.7 Bootstrap wording

    The bootstrap confidence interval reflects uncertainty by resampling from the observed data. It cannot correct biased sampling or an inappropriate observational unit.

### 15.8 Bayesian wording

    The posterior probability combines the prior/base rate with the evidence from the test result. It answers the probability of the condition given the observed evidence.

---

## 16. Request-to-code mapping

Use this mapping internally to select the correct code section.

| User asks for | Return section |
|---|---|
| import / read data | 3.2 |
| inspect / check data | 3.2, 3.3 |
| convert to factor | 3.4 |
| summary table | 3.5 |
| boxplot | 4.1 or 4.2 |
| interaction plot | 4.2 |
| paired plot | 4.3 |
| observed vs expected plot | 4.5 |
| proportion plot | 4.6 |
| scatterplot/regression plot | 4.7 |
| time plot | 4.8 |
| t-test | 5.1 or 5.2 |
| one-way ANOVA | 6.1 |
| two-way ANOVA | 6.2 |
| paired treatment analysis | 6.3 |
| Mendelian ratio | 7.2 or 7.3 |
| chi-square GOF | 7.1 |
| chi-square independence | 7.4 |
| bootstrap proportions | 8.1 |
| bootstrap mean/median | 8.2 or 8.3 |
| bootstrap difference | 8.4 |
| linear regression | 9.1 |
| multiple regression | 9.2 |
| interaction regression | 9.3 |
| correlation | 9.4 |
| logistic regression | 10.1 or 10.2 |
| Poisson regression | 10.3 |
| time trend | 11.1, 11.2 |
| Bayes / diagnostic test | 12.1–12.3 |
| clustering | 13.1–13.4 |
| power | 14.1–14.3 |
| interpretation wording | 15 |

---

## 17. Final self-check before replying

Before giving the user code, check:

1. Did I identify the requested code branch?
2. Did I use the user's dataframe name?
3. Did I use the user's variable names?
4. Did I avoid unrelated code?
5. Is the code runnable in R?
6. Did I include `set.seed()` if simulation/bootstrap/k-means is used?
7. Did I avoid chi-square for non-independent data?
8. Did I avoid endpoint-only trend analysis?
9. Did I keep the explanation concise?
10. Did I return only the requested reference-bank section unless a full workflow was requested?
