# Fraud Detection Project with Cassandra and Machine Learning

## Overview
This project aimed to detect fraudulent transactions using machine learning models. The dataset was stored in **Apache Cassandra**, a NoSQL database chosen for its ability to handle high-throughput data ingestion and retrieval in real-time. To address class imbalance and improve model performance, we applied **SMOTE** (Synthetic Minority Oversampling Technique) and tested multiple machine learning algorithms, including **Logistic Regression**, **SGDClassifier**, and **LightGBM**.

---

## Why Cassandra?
- **Scalability**: Cassandra can handle large amounts of data across multiple nodes without compromising performance.
- **High Throughput**: It excels at batching fast insertions and real-time retrieval, which is ideal for streaming transaction data.
- **Fault Tolerance**: Its distributed nature ensures data availability even in the event of node failures.
- **Batching in Cassandra**: Cassandra allows fetching data in manageable chunks, which was critical for training machine learning models incrementally.

### Problems Faced with Cassandra
- **ReadFailure**: We encountered Cassandra read failures when executing queries like `SELECT COUNT(*)` due to the large size of the data.
- **Batch Size**: Initially, we processed data in batches of 50,000 rows, but this caused inefficiencies for certain models like Logistic Regression, which required larger data sizes to generalize well. To address this, we increased the batch size to 150,000 rows.

---

## Machine Learning Challenges
1. **Imbalanced Dataset**: Fraudulent transactions constituted a very small fraction of the dataset, skewing model performance toward the majority class (non-fraudulent transactions).
2. **Incremental Learning**: Training models on batches of data required careful handling to ensure the model retained knowledge from previous batches.
3. **Model Limitations**:
   - **Logistic Regression**: Does not natively support incremental learning, making batch-wise training less efficient.
   - **LightGBM**: Faced issues when continuing training with subsequent batches due to constraints on splits with positive gain.
   - **SMOTE Integration**: While SMOTE improved class balance, it introduced computational overhead.

---

## Methodology
### 1. Data Retrieval
- Used **Apache Cassandra** to retrieve transaction data in batches.
- Addressed read failures by optimizing queries and monitoring paging states for seamless retrieval.

### 2. Handling Class Imbalance
- **SMOTE** was applied to oversample the minority class (fraudulent transactions), creating a balanced dataset for training.

### 3. Models Used
#### Logistic Regression
- A linear model for binary classification.
- Struggled with smaller batch sizes, requiring us to increase the batch size to 150,000 rows.
- Improved significantly after applying SMOTE.

#### SGDClassifier
- Supports incremental learning via the `partial_fit` method.
- Efficient for large datasets but required hyperparameter tuning for convergence.

#### LightGBM
- A gradient-boosting framework that is highly efficient for imbalanced datasets.
- Faced warnings regarding splits with no positive gain but delivered the best results after SMOTE.

---

## Key Problems and Solutions
- **Batch Size for Logistic Regression**: Initially, batches of 50,000 rows led to poor generalization. Increasing the batch size to 150,000 improved performance.
- **Incremental Training with LightGBM**: Faced challenges in reinitializing the model with new batches due to split constraints. Careful parameter tuning and SMOTE helped overcome this.
- **Class Imbalance**: The high class imbalance required using SMOTE for all models to ensure that fraudulent transactions were not overlooked.
- **Confusion Matrix Comparisons**: Visualized the confusion matrices for all models to analyze their performance in terms of true positives, true negatives, and false predictions.

---

## Results
### Logistic Regression
- Without SMOTE: Poor recall for fraudulent transactions.
- With SMOTE: Recall improved significantly but introduced some false positives.

### SGDClassifier
- Without SMOTE: Handled incremental learning efficiently but struggled with imbalanced data.
- With SMOTE: Improved recall and F1-score for fraudulent transactions.

### LightGBM
- Without SMOTE: Delivered high precision but low recall for fraudulent transactions.
- With SMOTE: Achieved the best balance between precision and recall, making it the most effective model.

### Confusion Matrix Comparison
A detailed analysis of the confusion matrices revealed that **LightGBM with SMOTE** consistently delivered the best performance across all metrics.

---

## Key Learnings
1. **Cassandra’s Strengths**: Its ability to batch and retrieve data efficiently made it an ideal choice for this project.
2. **SMOTE**: Essential for addressing the class imbalance and improving recall for fraudulent transactions.
3. **Incremental Learning**: Necessary for handling large datasets that could not fit into memory.
4. **Model Limitations**: Understanding the constraints of each model (e.g., Logistic Regression’s batch size dependency, LightGBM’s split warnings) was critical to optimizing performance.

---

## Future Work
1. **Hyperparameter Optimization**: Fine-tune parameters for all models to improve performance further.
2. **Real-Time Predictions**: Deploy the best-performing model for real-time fraud detection using a stream-processing tool like Apache Kafka.
3. **Explainability**: Incorporate SHAP or LIME to explain model predictions.
4. **Model Deployment**: Use Flask or FastAPI to serve the trained model as an API.

---

## Conclusion
This project demonstrated the application of Apache Cassandra and machine learning techniques for detecting fraudulent transactions. By addressing challenges such as class imbalance, batch training, and model constraints, we developed a robust pipeline. Among the tested models, **LightGBM with SMOTE** emerged as the most effective, balancing precision and recall to detect fraud accurately.

