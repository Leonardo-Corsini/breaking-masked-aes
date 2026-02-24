# Breaking Masked AES: A Machine Learning & Deep Learning Side-Channel Attack

![Python](https://img.shields.io/badge/Python-3.11%2B-blue)
![TensorFlow](https://img.shields.io/badge/TensorFlow-2.x-orange)
![Scikit-Learn](https://img.shields.io/badge/Scikit--Learn-Latest-yellow)
![License](https://img.shields.io/badge/License-MIT-green)

## Project Overview
This repository contains my AI University project for performing a Side-Channel Attack (SCA) against a hardware implementation of AES-128 protected with masking. Such hardware implementations of cryptographic algorithms could present vulnerabilities, creating high-risk scenarios for many devices. 

The primary goal of this project is to retrieve one byte of the encryption key by analyzing the electromagnetic current leakage during the S-BOX execution. To achieve this, I compare classic Machine Learning algorithms (Random Forest and Gaussian Naive Bayes) with Deep Learning (Multi-Layer Perceptron).

## Dataset
This project uses the public reference **ASCAD Dataset**, in the fixed-key, with no desync implementation.
* Contains electromagnetic measurements extracted from an 8-bit microcontroller executing a masked AES-128 algorithm.
* **50,000** training traces and **10,000** test traces.
* **700** features per trace.
* Fixed encryption key, but traces are protected by boolean masking with a randomly different mask applied to every trace.

> **Note:** Due to file size constraints, the ASCAD dataset is not included in this repository. Please download it from the official ASCAD database and place it in the `data/` folder.

## Methodology

### 1. Defeating the Masking Countermeasure
Initial data exploration showed a quite balanced distribution of classes. However, analyzing the first-order leakage using Pearson Correlation (modeled with the Hamming Weight of the unmasked signal) resulted in no correlation peaks due to the masking. 

To bypass this, a **2nd-order preprocessing technique** (Mean-Free Squaring) was applied to the traces for the classical ML models, making the correlation visible. Deep Learning (MLP), on the other hand, was trained on raw traces without any preprocessing operations, to let the model learn the non-linear combinations autonomously.

### 2. Dimensionality Reduction & Feature Selection
Two different non-linear techniques were tested for feature selection: Mutual Information and Random Forest feature importance.
Then, two machine learning models were trained on the selected features:
* **Random Forest (RF):** Trained using the 100 best features selected. Pre-pruned to combat overfitting.
* **Gaussian Naive Bayes (GNB):** Trained using the 20 best features. PCA was applied after feature selection to align with the model's assumption of independent features, leading to a performance boost.

## Results
Models were evaluated using **Key Rank** and **Success Rate** metrics, as standard Accuracy is <1% due to the very low Signal-to-Noise Ratio (SNR). Despite the low accuracy, the probability of finding the key in one guess remains higher than random guessing (~0.39%).

Key findings:
* **Deep Learning Dominance:** Machine Learning models are outperformed by Deep-Learning in this scenario. The Multi-Layer Perceptron can retrieve the key in **< 200 traces** on average. These obtained results are comparable with the literature.
* **Classical ML Performance:** The best obtained implementations of Random Forest and Gaussian Naïve Bayes were able to reach a stable rank 0 with **~1000** and **~3000** traces on average, respectively.
* **Feature Selection:** Random Forest feature selection proved to be the most effective for classical models. Mutual Information feature selection was slower to reach a stable rank 0 compared to Random Forest, but still permitted retrieving the key.

