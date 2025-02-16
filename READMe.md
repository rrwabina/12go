# Exploratory Data Analysis: Fraud Detection for 12GO

######## This README provides a comprehensive summary and discussion of our exploratory data analysis and modeling results. The insights cover data characteristics, customer profiles, fraud profiles, and revenue/net price analysis.
---

## Summary & Discussion

### Data Profile
- Dataset Imbalance:
  - Fraudulent transactions account for only **0.23%** of the entire dataset, making it highly imbalanced.
  - There are **81 cases** with negative net prices, such that **80 cases** are non-fraudulent and only **1 case** is flagged as fraud.
  - These cases involve **79 unique customers**, with the majority booking **1 or 2 seats** (31 cases for 1 seat and 43 cases for 2 seats), indicating isolated incidents likely due to refunds or data entry errors.

### Fraud Profile
- **Temporal Patterns:**
  - Fraud is most pronounced during early morning hours, with notable variations across specific times. 
  - At 5 AM, the fraud rate reaches 0.61% with 241 cases, while 4 AM exhibits a slightly lower rate of 0.52% with 246 cases. 
  - Similarly, at 6 AM, the fraud rate is 0.47% with 212 cases. Although 1 AM records the highest case count with 373 cases, its fraud rate is comparable to those of the other early morning hours.

- **User Acquisition Channels:**
  - **Email channel** exhibits the highest fraud rate at **0.82%**.
  - **Direct** and **Organic** channels follow with fraud rates of **0.55%** and **0.34%**, respectively.
  - **Paid** and **Partner** channels record the lowest fraud rates.

- **Security Flags and Fraud:**
  - **Proxy:** Fraud rate of **0.44%** (flagged) vs. **0.25%** (not flagged).
  - **TOR:** Fraud rate of **1.64%** (flagged) vs. **0.36%** (not flagged).
  - **VPN:** Fraud rate of **0.68%** (flagged) vs. **0.30%** (not flagged).
  - **Recent abuse:** Fraud rate of **0.64%** (flagged) vs. **0.26%** (not flagged).
  - Certain channels (e.g., **Partner** and **Unknown**) have extremely high security flag percentages even if their overall fraud rates appear lower, indicating potential additional internal verification.

### Revenue / Net Price Situation

- **Net Price Analysis:**
  - Fraudulent transactions have a **notably higher median net price** compared to non-fraudulent ones, along with a wider interquartile range.
  
- **High Net Price Records:**
  - There are **34,615 transactions** with net prices above THB 4,400 (99th percentile).
  - Within this high-value segment:
    - **32,780 transactions** are non-fraudulent.
    - **1,835 transactions** are fraudulent, indicating an elevated fraud presence.
  - These high-value transactions primarily belong to premium services:
    - **Aviation:** 22,272 records.
    - **Charter:** 8,977 records.
    - **Train:** 2,642 records.
  - The seat distribution for these records is largely for **1 seat (20,925 records)** or **2 seats (10,944 records)**.
---

## Modeling Recommendations (based from EDA)
- **Addressing the Imbalance:**
  - Use evaluation metrics beyond accuracy (e.g., precision, recall, F1-score, ROC-AUC).
  - Use weighted loss functions to assign higher penalties to misclassified fraud cases ensures that the model prioritizes fraud detection without overfitting to the dominant class
  - Consider oversampling, undersampling, or cost-sensitive learning to tackle the 0.23% fraud rate.
  - The dataset contains a lot of categorical variables, implying that SMOTE is not suitable for our analysis since it uses KNN to find the neighbours based on Euclidean distances. These distances are meaningless for categorical or discrete variables.

- **Key Features to Include:**
  - **Email-based Features:** Email domain score and Email score.
  - **Transaction Features:** Insurance flag (odds ratio ≈2.76) and Payment attempts (odds ratio ≈1.48).
  - **Security Indicators:** 
    - Proxy (odds ratio ≈3.29)
    - TOR (odds ratio ≈6.97)
    - VPN (odds ratio ≈3.76)
    - Recent abuse (odds ratio ≈4.00)
  - **Additional Transaction Details:** Seats, vehclass_id, and netprice_thb.
  - **Customer Attributes:** Passenger passport score, passenger score, payer country, role_id, channel, and nationality.
  - **Exclude Highly Collinear Variables:** Time-related variables such as createdon, godate, and paidon should be removed due to multicollinearity.

## Modeling Performance
- Four machine learning models—HistGradientBoostingClassifier, XGBoost, Isolation Forest, and LightGBM—were evaluated for their performance in fraud detection using a transactional dataset.
- HistGradientBoostingClassifier emerged as the most promising model, achieving the highest accuracy (94.1%) and AUC ROC score (0.957).
- XGBoost and LightGBM demonstrated comparable performance, with only marginal differences in accuracy and recall.
- Isolation Forest, however, exhibited a considerably lower AUC ROC score, indicating its lower effectiveness compared to the other models.
- Despite the high accuracy and recall achieved by the top-performing models, they all obtained low precision for the fraud class, resulting in a high number of false positives, potentially leading to unnecessary investigations and impacting customer experience.
- Deep learning models were excluded since they often underperform on tabular data due to challenges in handling mixed data types, high dimensionality, and sparse patterns, which hinder gradient calculation and lead to suboptimal performance.


## Conclusion: Business Insights and Recommendations for 12GO

- **Targeted Fraud Prevention:**
  - Enhance monitoring during early morning hours (notably around **5 AM, 4 AM, and 6 AM**).
  - Focus efforts on high-risk channels such as **Email** and **Direct**, and pay special attention to channels with high security flag percentages (e.g., **Partner** and **Unknown**).

- **Strengthen Security Measures:**
  - Flag proxy, VPN, and TOR usage for additional verification.
  - Restrict access or apply enhanced authentication for users with high-risk email domains and payment attempts.

- **Revenue Management:**
  - Leverage the insight that fraudulent transactions tend to have higher and more variable net prices.
  - Incorporate **transaction value** as a key parameter in the fraud detection model.
  - Apply segmentation or transformation techniques to manage the impact of high-value transactions, especially those above THB 4,400.
      - Apply stricter risk assessment to Aviation and Charter bookings above THB 4,400

- **Operational Efficiency:**
  - Implement stricter verification processes for transactions flagged with security indicators.
  - Allocate resources based on channel-specific risk profiles to optimize fraud detection and reduce financial losses.


