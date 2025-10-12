# <h1 align="center">Anomaly-Detection-in-Numerical-Data
<p align="center">
  <img width="720" height="405" alt="image" src="https://github.com/user-attachments/assets/63cff2b0-1b08-4a49-ab7d-bf8b7dbc76d5" />
</p>

This project consists of a modular architecture to evaluate anomaly detection methods based on Benford’s Law. The system aims to overcome the difficulty that exists in objectively comparing different methods due to the lack of reference data verified in real data sets.

The first module is a synthetic data generator that produces datasets with known anomaly patterns, allowing controlled experimentation and objective evaluation. This generator is customizable, allowing the manipulation of specific variables (types of anomalies, proportions, data dimensions) keeping the other factors constant.

The second module consists of an unsupervised classification model based on statistical tests for compliance with Benford’s Law. The statistical detection methods are the chi-square test, mean absolute deviation (MAD), Kolmogorov-Smirnov test, Euclidean distance, Hellinger distance and Kullback-Leibler divergence. Additionally, it uses the Fisher combination method to aggregate results and potentially improve overall performance. This model evaluates each instance individually, applying multiple statistical metrics to identify significant deviations indicators of anomalies.

The complete pipeline integrates synthetic data generation and classification model. It also generates comprehensive evaluations including precision, recall, F1-scores and ROC curves. 

Routines were created for sensitivity analysis, allowing to identify optimal operating conditions and compare the different methods under controlled conditions

## Data generator

The data generator generates a dataset with a quantity of features defined by parameter 𝑚 and a quantity of instances controlled by parameter 𝑛.
The generated dataset contains a 𝑡B ratio of anomalous rows with a 𝑡𝑚 ratio of anomalous
elements.

<p align="center">
  <img width="553,5" height="401,25" alt="image" src="https://github.com/user-attachments/assets/ae38e6c1-cf34-4f77-95b4-c0f221fb15b6" />
</p>

#### Step 1a.Data creation according to Benford’s Law

A data row is created with numbers generated from a uniform distribution between 0 and 1 which are then transformed by raising 10 to these values, so that they follow the Benford’s Law. That is, if 𝑥 ∈ 𝒰(0, 1) then log10(𝑥) follows a logarithmic distribution. After the generation of the row, or instance, according to the BL, it receives a label with the value 0, indicating that it does not contain anomalies. Then, several other rows without anomalies are generated, always using the same method, by adding the 0 label to these rows until the ratio given by 1-𝑡B is achieved (i.e., (1 − 𝑡B) 𝑛 rows without anomalies).

#### Step 1b. Data creation with anomalies

After that, the generator creates rows that simulate instances with anomalies whose amount depends on the parameter 𝑡B(ratio of anomalous rows). For these, it generates a combination of numbers based on Benford’s Law, in proportion of 1-𝑡𝑚 ((1 − 𝑡𝑚)𝑚 numbers), and anomalous numbers according to the parameter 𝑡𝑚 (ratio of anomalies in each anomalous row, which corresponds to 𝑡𝑚𝑚 numbers). The anomalous row can originate in a uniform distribution, Gaussian noise, uniform noise, mixed noise (uniform + Gaussian) or they can be simple random outliers. Uniform distribution anomalies are obtained by a uniform continuous distribution between 0 and 100000. To create the anomalies based on Gaussian noise, the generator first generates data according to the Benfords Law, then generates the Gaussian noise based on a standard distribution between 0 and the 𝑖𝑛𝑡𝑒𝑛𝑠𝑖𝑡𝑦 parameter and at the end adds up the conforming data, with the noise. Generator creates the anomalies based on Uniform noise in the same way but instead of a normal distribution it uses a uniform distribution between −𝑖𝑛𝑡𝑒𝑛𝑠𝑖𝑡𝑦 and +𝑖𝑛𝑡𝑒𝑛𝑠𝑖𝑡𝑦. To generate mixed noise, the generator sums Benfords Law data with Gaussian noise data and Uniform noise data. All of anamalous rows are labeled 1, indicating the presence of anomalies.

#### Step 2.Shuffle

All generated data is organized into a DataFrame, which is then shuffled randomly to ensure that the examples with and without anomalies are mixed together.

#### Step 3.Final dataset

Finally, the DataFrame obtained is converted into a NumPy array, making the data ready for use.

#### Data Generator Algorithm

<img width="492" height="748" alt="image" src="https://github.com/user-attachments/assets/b0240781-51fa-4f44-abf4-19f9cb57f7c5" />

## Classification Model

The developed classification model implements an unsupervised approach, focused on the detection of anomalies based on statistical discrepancy with respect to the Benford distribution, without the use of supervised training or conventional machine learning. It was implemented in Python, using the following libraries:
- NumPy for numerical operations and array manipulation,
- SciPy for statistical tests (chi-square),
- Pandas for data handling and reporting performance metrics,
- Custom functions for metrics such as MAD, Euclidean, Hellinger, and KL divergence.

The system is organized into modular components following a sequential pipeline. It involves the sequential application of different statistical tests and distance measurements, whose results are subsequently combined to generate predictions and performance metrics. 

<p align="center">
  <img width="642" height="674" alt="image" src="https://github.com/user-attachments/assets/b259b198-2ef3-4f43-bdd5-0cf59fdde18b" />
</p>

The system was designed to process each observation (row) independently, testing whether the empirical distribution of first digits conforms to the theoretical distribution defined by Benford’s Law. The procedure begins with the extraction of the first digits from each numerical value of the analyzed row (Extraction of the first digits), followed by the calculation of the frequency of occurrence of each digit (Digit frequency calculation). This observed distribution is compared with the theoretical distribution of Benford’s Law through six statistical and distance methods:
- Chi-square test (Pearson’s chi-square),
- Mean absolute deviation (MAD),
- Kolmogorov-Smirnov (KS),
- Euclidian distance,
- Hellinger distance,
- Divergence of Kullback-Leibler.

Each metric captures different aspects of deviation, from cumulative distribution differences to point-wise distances and divergence measures. The diversity of metrics increases the detection capability under different manipulation patterns. After computing these metrics, their statistical significance is assessed through the corresponding 𝑝-values. Values lower than the significance level 𝛼 indicate the rejection of the null hypothesis and, therefore, the classification of the case as manipulated (class 1). So, for each test, the model:
- Generates the statistic and 𝑝-value,
- Applies a decision rule (𝑝-value < 𝛼 classified as anomalous),
- Records both the 𝑝-value and binary classification.

To avoid numerical problems, very low values are truncated to 10−15. The 𝑝-values obtained in the six tests are combined by the Fisher’s combination method, resulting in a single aggregated measure. For these tests a significance level, 𝛼, previously configured is used to produce a final classification. Both the individual results of each test and the combined result are used to generate predictions that, together with real classes, feed the construction of the confusion matrix. From this matrix, performance metrics such as recision, recall and F1-score are calculated, allowing to evaluate the effectiveness of the model. In addition, a variation of 𝛼 (0 to 1, with step of 0.001) is generated to enable the construction of the ROC curves and analysis of the discriminatory capacity of each approach. This architecture allows independent evaluation of each metric’s performance, as well as the combined effect via Fisher’s method. The code was designed to ensure modularity to allow future extension with additional metrics or alternative combination methods. The system processes each row independently in an iterative loop, storing predictions, 𝑝-values, and performance metrics in structured arrays and DataFrames for later analysis. The model’s predictions were compared to the known class labels using a confusion matrix for each metric and for Fisher’s method. From the confusion matrix, the following performance indicators were computed:
- True Positives (TP): correctly identified anomalous rows,
- True Negatives (TN): correctly identified non-anomalous rows,
- False Positives (FP): normal rows incorrectly flagged as anomalous,
- False Negatives (FN): anomalous rows missed by the model.

From these, precision, recall, and F1-score were calculated to provide a balanced view of classification performance. To complement the narrative description and improve the formal presentation of the proposed method, the Algorithm 2 outlines the process step-by-step. This structured representation facilitates replication and provides a clear overview of the workflow, from data input to the computation of performance metrics.

<img width="492" height="427,7388" alt="image" src="https://github.com/user-attachments/assets/b951a1b3-db58-4eb8-8c76-37a2bd50daf1" />

Importantly, the evaluation metrics (precision, recall, and F1-score) are standard in machine learning classification tasks. Their inclusion ensures that the model’s performance is directly comparable to traditional machine learning classifiers, enabling benchmarking and positioning the statistical approach within the broader ML context.

All performance results were organized into a Pandas DataFrame which can be used for reporting and visualization.

## Sensitivity analysis

A sensitivity analysis was conducted in order to evaluate the performance of different methods based on Benford’s Law in detecting numerical anomalies. The experimental process consisted of controlled generation of synthetic data, allowing to analyze the sensitivity and robustness of statistical tests in different experimental settings, following the steps that have already been previously explained. The kind of anomaly used in this experiments is the uniform data. In each simulation was played with the parameters explained in Table bellow.

<p align="center">
  <img width="613,5" height="196,5" alt="image" src="https://github.com/user-attachments/assets/aef7098d-7f28-44d5-9dce-0e783048c7a3" />
</p>

The experimental process was repeated by systematically varying one parameter at a time, keeping the remaining constant, in order to study the impact of different factors on the performance of the methods. The variables tested included the ratio of manipulations per row (𝑡𝑚), the ratio of manipulated rows (𝑡B), the total number of rows (𝑛) and the number of columns per row (𝑚). Each simulation produced results recorded in Excel files, containing the performance metrics obtained for each experimental configuration. This experimental design ensured comparability between the methods, since all were evaluated under the same conditions and using the same simulated data in each scenario. The use of synthetic data also allowed to ensure the previous knowledge of the real class of each set, ensuring a rigorous evaluation of the anomaly detection capacity of each tested method.

## Authors
- Patrícia Martinho - MSc in Data Science, School of Technology and Management, Polytechnic Institute of Leiria; Leiria, Portugal; 2230377@my.ipleiria.pt;
- Rui Santos - Department of Mathematics, School of Technology and Management, Polytechnic of Leiria; Leiria; Portugal; rui.santos@ipleiria.pt;
- Mário Antunes - Department of Computer Engineering, School of Technology and Management, Polytechnic of Leiria; Leiria; Portugal; mario.antunes@ipleiria.pt;

## Thesis Information

This project is part of a master thesis conducted at the Instituto Politécnico de Leiria, supervised by Prof. Rui Santos e Prof. Mário Antunes.

<img width="200" height="79" alt="image" src="https://github.com/user-attachments/assets/38cbb86d-157e-4495-8db6-7d49367c63ed" />

## Questions
For any questions, feel free to send an email: 2230377@my.ipleiria.pt


