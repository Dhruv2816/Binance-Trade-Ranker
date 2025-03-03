# Binance-Trade-Ranker

## Analysis and Ranking of Binance Accounts Based on Historical Trade Data

## 1. Introduction
This project analyzes 90 days of historical trade data from various Binance accounts. Each account is identified by a unique `Port_IDs` and has an associated `Trade_History` detailing individual trades (including timestamp, asset, side, price, quantity, and realized profit). The objective is to compute key financial metrics for each account, rank them using both traditional and PCA-based weighted approaches, and generate a top 20 list of accounts based on performance.

## 2. Methodology

### 2.1 Data Exploration and Cleaning
- **Data Loading:**  
  The dataset is loaded from a CSV file containing `Port_IDs` and `Trade_History`.  
- **Handling Missing Values:**  
  Missing values in `Trade_History` are replaced with an empty list representation (`"[]"`), ensuring consistency.
- **Data Transformation:**  
  The `Trade_History` column (stored as a string representation of a list) is converted into a Python list using `ast.literal_eval()`. The dataset is then exploded so that each trade becomes an individual row. Additionally, timestamps are converted from milliseconds to a standard datetime format for proper chronological ordering.

### 2.2 Feature Engineering
- **Trade Classification:**  
  A new feature, `trade_type`, is derived by combining the `side` (BUY/SELL) and `positionSide` (LONG/SHORT) fields, classifying trades into categories like `long_open`, `long_close`, `short_open`, or `short_close`. For accounts with no trade history, a default value of `"none"` is assigned.
- **Metric Calculation:**  
  For each account, the following metrics are computed:
  - **ROI (Return on Investment):**  
    Calculated as (PnL / total investment on BUY trades) × 100.
  - **PnL (Profit and Loss):**  
    The sum of `realizedProfit` across all trades.
  - **Sharpe Ratio:**  
    The ratio of the mean of `realizedProfit` to its standard deviation (assuming a risk‑free rate of 0).
  - **MDD (Maximum Drawdown):**  
    The largest decline observed in the cumulative profit curve.
  - **Win Rate & Win Positions:**  
    The ratio and count of trades with positive `realizedProfit`, respectively.
  - **Total Positions:**  
    The total number of trades executed.

### 2.3 Ranking Algorithm
- **Traditional Ranking:**  
  Each metric is individually ranked (with higher values being better for ROI, PnL, Sharpe Ratio, and Win Rate, and lower values preferred for MDD). The ranks are summed to form a composite score.
- **PCA-Based Weighted Ranking:**  
  - **Standardization and PCA:**  
    Key metrics (ROI, PnL, Sharpe Ratio, Win Rate, and MDD) are standardized. Since MDD is undesirable, it is inverted so that higher values indicate better performance. PCA is then applied to the standardized data to derive loadings from the first principal component.
  - **Deriving Weights:**  
    The PCA loadings (normalized) are used as data-driven weights for the metrics.
  - **Overall Score:**  
    Each metric’s z-score is multiplied by its corresponding weight and summed to compute a weighted overall score. Accounts are then ranked based on this score.

## 3. Challenges Faced

### 3.1 Handling Nested Data Structures
- **Conversion of Trade History:**  
  The `Trade_History` field required conversion from a string representation to a list of dictionaries. This step involved careful error handling and imputing missing values to ensure that each account’s trade data could be properly exploded and analyzed.

### 3.2 Sensitivity of PCA to Outliers
- **Influence of Outliers:**  
  Standard PCA is sensitive to outliers because it uses variance as a basis. Outliers in metrics such as ROI or PnL can skew the PCA loadings, resulting in disproportionate weights for certain metrics.
- **Mitigation Approaches:**  
  Options considered included robust scaling (using `RobustScaler`), capping extreme values, or employing robust PCA techniques. In this analysis, missing values were imputed with column means to reduce issues during PCA.

### 3.3 Data Standardization
- **Ensuring Comparability:**  
  Metrics were on different scales, requiring proper standardization (z-scoring) before applying PCA or combining them in a weighted score. Special handling was applied to MDD by inverting its values to align with the “higher is better” convention.

## 4. Results and Final Outputs

### 4.1 Calculated Metrics
- A CSV file (`calculated_metrics.csv`) was generated containing all computed financial metrics for each account, including ROI, PnL, Sharpe Ratio, MDD, Win Rate, Win Positions, and Total Positions.

### 4.2 Ranking of Accounts
- **Traditional Ranking:**  
  Accounts were ranked by summing individual metric ranks.
- **PCA-Based Weighted Ranking:**  
  Using PCA-derived weights, each account’s standardized metrics were combined to compute a weighted overall score. The accounts were then sorted in descending order of this score, yielding a top 20 list.

### 4.3 Visualizations
- Histograms and scatter plots were generated to illustrate the distribution of ROI, the relationship between ROI and Sharpe Ratio (with PnL as an indicator), and the frequency distribution of trade types.
  
### 4.4 Final Top 20 Accounts
- The top 20 accounts were identified based on the PCA-based weighted ranking, providing a data-driven perspective on account performance.

## 5. Conclusion
The analysis successfully processed and transformed Binance trade data, computed key performance metrics, and applied both traditional and PCA-based ranking algorithms. The PCA-based approach provided a robust, data-driven method for weighting metrics, although it highlighted challenges related to outlier sensitivity. The final deliverables include a complete Jupyter Notebook/Python script with code, a CSV file of calculated metrics, and a detailed report summarizing the methodology, challenges, and findings. This work lays the foundation for informed decision-making based on comprehensive performance analysis.


