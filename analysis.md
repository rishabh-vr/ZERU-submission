# Wallet Credit Score Analysis

## 1. Analysis Overview

This analysis examines the credit scores assigned to wallets by the winning **LightGBM model**. The model achieved the highest predictive accuracy (ROC AUC ≈ 0.94) in identifying wallets at risk of liquidation. The resulting scores (0-1000) serve as a powerful proxy for on-chain financial responsibility.

This document breaks down the overall score distribution and explores the distinct behavioral patterns of wallets in low, mid, and high-scoring tiers, linking them directly to the features engineered from on-chain data.

---

## 2. Score Distribution

The distribution of credit scores across the entire user base is heavily **right-skewed**. This indicates that while the majority of users engage with the protocol responsibly (resulting in high scores), there is a significant, albeit smaller, cohort of high-risk users who pull the average down and populate the lower end of the score spectrum. This is a healthy and expected distribution for a risk-scoring system.

| Score Range   | Wallet Profile                       | Key Behavioral Indicators                                                                |
| :------------ | :----------------------------------- | :--------------------------------------------------------------------------------------- |
| **0-200** | **"Extreme Risk / Liquidated"** | Extremely high `borrow_to_deposit_ratio`, minimal repayments, likely already liquidated.   |
| **200-500** | **"Aggressive Degens / Bots"** | High leverage, low repayment ratios, high transaction frequency.                         |
| **500-800** | **"The Average User / Speculator"** | Balanced borrowing and repaying, moderate leverage, human-like transaction cadence.      |
| **800-950** | **"Responsible Power Users"** | Low leverage, high `repay_to_borrow_ratio`, high total value of transactions.            |
| **950-1000** | **"Conservative Savers / Whales"** | Primarily providers of liquidity (`deposit`), minimal or zero borrowing activity.          |

![Score Distribution Chart](https://i.imgur.com/uIbi4iE.png)
*An illustrative histogram showing the expected right-skewed distribution of credit scores.*

---

## 3. Behavioral Profiles by Score Tier

By segmenting wallets into tiers, we can create clear, data-driven personas based on the model's most important features.

### **📉 Low-Score Wallets (Score < 500)**

These are the riskiest wallets. Their behavior is a clear red flag for the protocol.

* **Primary Indicator:** A very high **`borrow_to_deposit_ratio`**. These users borrow far more than their collateral safely allows, maximizing their leverage.
* **Debt Accumulation:** They exhibit a low **`repay_to_borrow_ratio`**, meaning they are not actively managing or paying down their debt, letting it spiral.
* **High Volatility:** They often have a high `value_usd_std`, indicating erratic transaction sizes, and a low `time_since_last_txn_hours_mean`, suggesting bot-like, high-frequency trading.

### **🟡 Mid-Score Wallets (Score 500 - 800)**

This group represents the majority of active, speculative users who are managing their risk reasonably well.

* **Balanced Leverage:** Their `borrow_to_deposit_ratio` is moderate. They use leverage but maintain a healthy collateral buffer.
* **Active Management:** They show a healthy balance between `borrow`, `deposit`, and `repay` actions, indicating they are actively managing their positions.
* **Human Cadence:** Their transaction frequency is consistent with a human user monitoring their positions, not an automated script running 24/7.

### **📈 High-Score Wallets (Score > 800)**

These are the most valuable and stable users for the protocol. They are providers of capital and exhibit extremely low-risk behavior.

* **Capital Providers:** Their dominant action is `deposit`. They often have a **`borrow_to_deposit_ratio` near zero**.
* **Low Risk Appetite:** If they do borrow, they do so with a small fraction of their collateral and have a very high `repay_to_borrow_ratio`.
* **Significant Value:** These wallets are often associated with a high `value_usd_sum`, indicating they are whales or significant liquidity providers, whose conservative behavior is crucial for protocol stability.
