# Anomaly-Detection-in-Numerical-Data

<img width="283" height="197" alt="image" src="https://github.com/user-attachments/assets/000b7579-3767-4519-9053-468d08ea95ca" />

This project consists of a modular architecture to evaluate anomaly detection methods based on Benford’s Law. The system aims to overcome the difficulty that exists in objectively comparing different methods due to the lack of reference data verified in real data sets.

The first module is a synthetic data generator that produces datasets with known anomaly patterns, allowing controlled experimentation and objective evaluation. This generator is customizable, allowing the manipulation of specific variables (types of anomalies, proportions, data dimensions) keeping the other factors constant.

The second module consists of an unsupervised classification model based on statistical tests for compliance with Benford’s Law. The statistical detection methods are the chi-square test, mean absolute deviation (MAD), Kolmogorov-Smirnov test, Euclidean distance, Hellinger distance and Kullback-Leibler divergence. Additionally, it uses the Fisher combination method to aggregate results and potentially improve overall performance. This model evaluates each instance individually, applying multiple statistical metrics to identify significant deviations indicators of anomalies.

The complete pipeline integrates synthetic data generation and classification model. It also generates comprehensive evaluations including precision, recall, F1-scores and ROC curves. 

Routines were created for sensitivity analysis, allowing to identify optimal operating conditions and compare the different methods under controlled conditions

## Data generator

The data generator generates a dataset with a quantity of features defined by parameter 𝑚 and a quantity of instances controlled by parameter 𝑛.
The generated dataset contains a 𝑡B ratio of anomalous rows with a 𝑡𝑚 ratio of anomalous
elements.

<img width="738" height="535" alt="image" src="https://github.com/user-attachments/assets/ae38e6c1-cf34-4f77-95b4-c0f221fb15b6" />
jsgfdjgsjdgf

### Step 1a.Data creation according to Benford’s Law

A data row is created with numbers generated from a uniform distribution between 0 and 1 which are then transformed by raising 10 to these values, so that they follow the Benford’s Law. That is, if 𝑥 ∈ 𝒰(0, 1) then log10(𝑥) follows a logarithmic distribution. After the generation of the row, or instance, according to the BL, it receives a label with the value 0, indicating that it does not contain anomalies. Then, several other rows without anomalies are generated, always using the same
method, by adding the 0 label to these rows until the ratio given by 1-𝑡B is achieved (i.e., (1 − 𝑡B) 𝑛 rows without anomalies).

### Step 1b. Data creation with anomalies




<img width="246" height="374" alt="image" src="https://github.com/user-attachments/assets/b0240781-51fa-4f44-abf4-19f9cb57f7c5" />
