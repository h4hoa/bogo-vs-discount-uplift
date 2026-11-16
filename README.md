# BOGO vs. Discount: Measuring Promotion Uplift with an A/B/n Test

Which promotion actually *causes* more customers to buy - "Buy One Get One" or a discount - and for whom?
This project estimates the **incremental uplift** of two promotional offers using a randomized three-arm experiment (A/B/n test) on 64,000 retail customers.

## Business question

A retailer ran two promotions and wants to know:
1. What is the incremental uplift of each promotion on purchases?
2. Which offer returns the higher uplift?
3. Which customers should receive which offer?

## Data

64,000 customers, one row each. No missing values or duplicate customers.

**Source:** public Kaggle dataset - [Marketing Promotion Campaign Uplift Modelling](https://www.kaggle.com/datasets/davinwijaya/customer-retention) (license: LICENSE). The business framing follows a typical promotion-uplift case question.

| Variable | Description |
|---|---|
| `recency` | Months since last purchase (1-12) |
| `historical customer spend` | Past spend ($), right-skewed (median ≈ $158, mean ≈ $242) |
| `has_used_discount_in_the_past` / `has_used_bogo_in_the_past` | Past promotion use (0/1) |
| `Urbanity` | Urban / Suburban / Rural |
| `is_referral` | Acquired through a referral (0/1) |
| `channel` | Web / store / Multichannel |
| `offer` | **Treatment:** No Offer / Buy One Get One / Discount (≈ 21.3k each) |
| `conversion` | **Outcome:** purchased (0/1) |

## Approach

1. **Experiment design.** Offers were randomly assigned, so the No Offer group gives the counterfactual: what would have happened without a promotion.
2. **Randomization check.** Compared all baseline traits across the three arms using standardized mean differences (SMD) and distribution plots. 
3. **Uplift estimation.** Linear probability model with No Offer as the reference:
   ```r
   lm(conversion ~ offer, data = df)
   ```
   Each coefficient is that offer's incremental uplift in percentage points. Releveling to Discount tests BOGO vs. Discount directly.
4. (TBD) **Robustness.** Add customer covariates and fit a logistic regression as a check.
5. (TBD) **Heterogeneous treatment effects.** One pre-specified interaction model per segment (`offer * channel`, `offer * urbanity`, `offer * used_bogo`, `offer * used_discount`, `offer * referral`), each tested with a joint F-test before interpreting individual terms.

## Limitations

- **Conversion only.** No revenue, order value, promotion cost or margin data, so uplift is measured in purchases, not dollars or profit.
- **Unknown campaign details.** Discount size, campaign dates, conversion window and randomization procedure are not documented.
- **Single snapshot.** Cannot detect pull-forward purchases or long-term effects.
- **Multiple comparisons.** Segment findings come from several tests and should be treated as hypotheses to confirm.

## Next steps

- Build an uplift model to assign each customer the offer with the highest expected gain.

## Tools

R (readxl, dplyr, ggplot2, tableone) · A/B/n testing · linear probability and logistic models · heterogeneous treatment effects
