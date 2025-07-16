# Credit_Score_DeFi

## Objective
Create a system that assigns a **credit score (0–1000)** to each wallet address based only on its **historical transaction behavior** on the Aave V2 protocol (Polygon network). This score helps identify how safe or risky a wallet is based on what actions it performs, such as `deposit`, `borrow`, `repay`, or `liquidationcall`.

---

## Methodology: Behavior-Based Scoring (No ML)

Instead of using Machine Learning, we use a **simple, explainable, rule-based formula**.

Why?

-  We don’t have labels like "good" or "bad" users — so supervised ML isn’t possible.
-  ML clustering gave poor interpretability and score spread.
-  This approach is:
  - Easy to debug
  - Easy to fetch exact results
- Tried using ML Unsupervised Learning Algorithms like K Means and DBSCAN but it work out well.

---

##  Features Used

Each wallet is grouped and analyzed for:

| Feature | Description |
|--------|-------------|
| `total_deposit_usd` | How much money was deposited (converted to USD) |
| `total_borrow_usd` | Total amount borrowed in USD |
| `total_repay_usd` | Total amount repaid in USD |
| `num_liquidation` | Times the wallet was liquidated |
| `total_transactions` | Total number of Aave actions |
| `repay_ratio` | repay / borrow |
| `deposit_borrow_ratio` | deposit / borrow |
| `borrow_per_txn` | borrow / total txns |

---

## Scoring Formula (Out of 1000)

Each wallet starts with a **base score of 300**, then we add or subtract based on responsible behavior:

credit_score = 300
+ (repay_ratio * 300)[: max 300]
+ (100 if deposit_borrow_ratio > 1 else deposit_borrow_ratio * 100)
+ (100 if borrow_per_txn < 0.5 else max(0, 100 - borrow_per_txn * 100))
- (num_liquidation * 50)[: max -200]


| Metric                     | Max Score Impact |
|----------------------------|------------------|
| High `repay_ratio`         | +300             |
| Low `num_liquidation`      | +200             |
| More deposits than borrows | +100             |
| Less borrowing per txn     | +100             |
| Base score for all wallets | +300             |

All scores are clipped between **0 and 1000**.

---


