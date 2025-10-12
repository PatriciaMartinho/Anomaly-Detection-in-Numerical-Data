# Anomaly-Detection-in-Numerical-Data

This project consists of a modular architecture to evaluate anomaly detection methods based on Benford’s Law. The system aims to overcome the difficulty that exists in objectively comparing different methods due to the lack of reference data verified in real data sets.

The first module is a synthetic data generator that produces datasets with known anomaly patterns, allowing controlled experimentation and objective evaluation. This generator is customizable, allowing the manipulation of specific variables (types of anomalies, proportions, data dimensions) keeping the other factors constant.

The second module consists of an unsupervised classification model based on statistical tests for compliance with Benford’s Law. The statistical detection methods are the chi-square test, mean absolute deviation (MAD), Kolmogorov-Smirnov test, Euclidean distance, Hellinger distance and Kullback-Leibler divergence. Additionally, it uses the Fisher combination method to aggregate results and potentially improve overall performance. This model evaluates each instance individually, applying multiple statistical metrics to identify significant deviations indicators of anomalies.

The complete pipeline integrates synthetic data generation and classification model. It also generates comprehensive evaluations including precision, recall, F1-scores and ROC curves. 

Routines were created for sensitivity analysis, allowing to identify optimal operating conditions and compare the different methods under controlled conditions.

## Data generator

