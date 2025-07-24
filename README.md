# DeFi Wallet Credit Scoring Engine

## 1. Project Overview

This repository contains a comprehensive machine learning pipeline to assign a credit score (0-1000) to DeFi wallets based on their historical transaction data from the Aave V2 protocol. The project's goal is to create a reliable measure of on-chain reputation, where higher scores indicate responsible financial behavior and lower scores signify high-risk or potentially malicious activity.

The solution moves beyond a single model, employing a **rigorous benchmarking framework** to compare classical, neural network, and transformer-based architectures. This ensures the final scoring logic is derived from the most performant and suitable model for the task.

---

## 2. Architecture & Processing Flow

The system is architected as a sequential pipeline that ingests raw data, engineers a rich feature set, and then systematically evaluates multiple model types before generating a final score.

![Flowchart](https://i.imgur.com/bQ9jY5J.png)
*A high-level overview of the multi-model benchmarking pipeline.*

### **Step 1: Data Ingestion & Feature Engineering**
The script ingests the raw JSON transaction log and flattens it into a workable format. The core of the system's intelligence lies in its feature engineering, where raw event data is transformed into a high-dimensional feature profile for each wallet. Key engineered features include:
* **Value Aggregates:** `sum`, `mean`, `max`, and `std` of transaction values in USD.
* **Transactional Cadence:** `mean`, `max`, and `std` of the time between transactions.
* **Behavioral Ratios:** Critical risk indicators like `borrow_to_deposit_ratio` and `repay_to_borrow_ratio`.
* **Action Fingerprint:** Counts of each distinct action (`deposit`, `borrow`, `repay`, etc.).

### **Step 2: Model Benchmarking**
Instead of relying on a single algorithm, the pipeline trains and evaluates three distinct classes of models to ensure the best possible approach is chosen. The target variable for all models is `was_liquidated`, a binary flag indicating if a wallet has ever defaulted.

1.  **Classical Models:** This suite serves as a powerful baseline and includes:
    * **Logistic Regression:** A simple, interpretable baseline.
    * **Random Forest:** A robust ensemble model.
    * **LightGBM (🏆 Winner):** A high-performance gradient boosting model tuned with **Optuna**. It consistently delivered the best performance and was selected for the final scoring.

2.  **Deep Learning (Neural Network):** A feed-forward neural network built with PyTorch, designed to capture complex non-linear interactions between features.

3.  **Cutting-Edge (Transformer):** A modern architecture using self-attention mechanisms, built to weigh the importance of different features dynamically.

### **Step 3: Credit Score Generation**
A credit score is generated independently by each model type to demonstrate its output. The final, official score is derived from the **winning LightGBM model** due to its superior performance (AUC ≈ 0.94) and efficiency. The scoring logic is transparent and directly tied to risk:

**Credit Score = (1 - P(Liquidation)) \* 1000**

Here, `P(Liquidation)` is the model's predicted probability that a wallet will be liquidated. This creates an intuitive scale where a score of 950 implies a 5% liquidation risk, and a score of 100 implies a 90% risk.

---

## 3. How to Run

The entire pipeline is contained within a single, self-contained Python script or Jupyter Notebook.

1.  **Install Dependencies:**
    The script includes a command to install all necessary libraries.
    ```bash
    pip install optuna lightgbm scikit-learn pandas numpy torch matplotlib seaborn tqdm
    ```

2.  **Place Data:**
    Ensure the `user-wallet-transactions.json` file is located in the same directory as the script.

3.  **Execute the Script:**
    Run the script or notebook cells from top to bottom. It will perform all steps automatically:
    * Install dependencies.
    * Load data and engineer features.
    * Train and evaluate all models, with visible progress bars for deep learning architectures.
    * Generate comparative plots.
    * Output a sample of scores from each model type.
