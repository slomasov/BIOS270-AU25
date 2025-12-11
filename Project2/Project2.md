# Project 2: Machine Learning

In this project, you will write a proposal for your bioinformatics/ML project

> *“Wow, my model has 97% accuracy!” - a happy moment just before realizing the test set had unintentionally leaked into the training data.*

---

## Project Proposal

Your proposal should include the following sections.

### 1. Project Overview

- **Overarching goal**  
  Clearly state the primary objective of the project.
    - Goal: develop federated learning methods for applications where bioinformatic data is available locally (e.g to hospitals) but cannot be shared due to privacy regulations

- **Rationale**  
  Explain why the project is important or interesting.  
  What problem does it address, and why is it worth solving?
    - Patient data often cannot be shared due to strict privacy regulations. As a result, large models cannot be deployed. A potential solution could be federated learning, which runs models locally and averages out parameters, preserving privacy. However, these aggregated models may perform poorly. So, developing an efficient model may allow to deploy large models that utilize all data. 

- **Specific aims** (at least two)  
  For each aim, provide:
  - A clear **aim statement**  
  - **Expected outcomes** - what you hope to demonstrate or achieve  
  - **Potential challenges** and how you plan to address them  
  
  - Aim 1: develop an efficient federated learning algorithm
    - Outcome: develop a model and with provable guarantees on runtime and storage efficiency and, if possible, confidence intervals on test set performance after averaging out parameters
    - Challenges: the main challenge may be statistical guarantees on federated performance. In many cases, even in the non-federated case, it is hard to provide statistical bounds for ML models. In the federated case, this may be even harder. One potential solution could be putting assumptions on the data distribution and/or choosing a simpler model, at the cost of generality. Another potential solution could be empirical verification on different datasets.

  - Aim 2: test the algorithm on real datasets
    - Outcome: empirically verify that a federated algorithm performs well (e.g doesn't lose too much accuracy going from local to federated models; performs better than existing alternatives; performs "well enough" on local data)
    - Challenges: first, such algorithm needs to be developed. Second, to my knowledfe, federated learning algorithms don't have great benchmarks, so comparing to existing algorithms may be hard. I think these could be solved by a lot of engineering work creating and evaluating these algorithms.   

### 2. Data

Describe the dataset you will work with and how you plan to manage it.

- **Dataset description**
  - **Source** (e.g., public repository, lab-generated, simulated, etc.)
  - **Size** (number of samples, features, storage)
  - **Format** (CSV, FASTQ, images, JSON, Parquet, etc.)

  - Source: since the goal is developing methodology, both public repo and simulated data can be used for testing. To have a higher focus on applications and potentially deploy a model, more clinical data could be collected.
  - Size: application-specific, probably O(100k) samples with O(100) features, at around O(1) GB 
  - Format: CSV should be sufficient

- **Data suitability**
  - Is the current format optimal for downstream processing? - most likely no, may come with missing data in some csv storage
  - What transformations or preprocessing steps will be necessary? - cleaning, converting to pandas/numpy files if they come from potentially messy databases

- **Storage and data management**
  - Where will you store the dataset? - Google drive should be sufficient
  - How will you back it up? - Drive back up
  - How will you share it with collaborators if needed? - Secure drive link


### 3. Environment

Document how your computational environment will be set up.

- **Coding environment**
  - Local machine? HPC? Jupyter notebook? code-server? - Local machine should be sufficient. If more data comes in, may rent a GPU

- **Dependencies**
  - Key packages, libraries, or tools required for your analysis. - pandas, numpy, matplotlib, scikit learn, pytorch should be sufficient for ML analysis, and may use some bio-specific packages depending on the data

- **Reproducibility**
  - How will you ensure others can rerun your analysis?
    - Version control  -store in git
    - Requirements file / environment.yml - micromamba environment should be sufficient  
    - Containerization  - having singularity should be sufficient


### 4. Pipeline

Describe the sequence of steps your analysis will follow.

- **Algorithms and methods**  
  What models, algorithms, or computational steps do you plan to run? Are there steps that depends on output of other steps? - Should modulate the pipeline for data processing (cleaning, re-formatting, etc.), local ML methods with baselines (e.g linear regression, random forestss, some NN methods), aggregating parameters after local methods have been run, re-running with the aggregated model, and potentially adding the functionality to iterate on these. We also need to have blocks for output storage and plotting (e.g as csv and png).

- **Scalability and efficiency**  
  How will you ensure your pipeline runs efficiently on your dataset size, format, number of samples? - We need to be careful loading data (e.g using hash tables or SQL queries) if we plan to iterate on extracting some elements, that may be the main computational bottleneck. We could also try to create checkpoints where we save the model state in case the algorihtm terminates or encounters an error and we want to continue; and we may want to add some de-bugging steps for smaller samples.


### 5. Machine Learning

Brainstorm an ML task that can be performed on your data

- **Task definition**  
  What is the supervised or unsupervised learning problem that appropriate for your data? - Depends on the exact task; could be supervised learning, e.g a classification problem from a certain sequence to health dangers.

- **Feature representation**  
  How will you convert raw data into numerical form suitable for modeling? - With this data size, reading as numpy or pandas -> numpy should be sufficient; if data grows, we may want to use hdf5.

- **Model selection**  
  Which model(s) will you apply and why? - should be baselines + novel models: could do logistic regression as the simplest baseline, compared to e.g random forests as another common choice, with and without regularization. After that, could apply NN models for more complexity, perhaps with some regularization on the number of features.

- **Generalization strategy** (for supervised learning)  
  How will you ensure your model performs well on unseen data? - we need to train/test efficiently, and have a good experimentation pipeline to iterate quickly. Regularization may generally be useful for these tasks, as in federated learning noise is increased with averaging, so zero-ing out parameters may help.

- **Evaluation metrics**  
  What metrics will you track? Why are they appropriate for your task? - accuracy could be the simplest metric as it generally reflects classification accuracy quite well (with some caveats like unbalanced datasets, in which case we may use things like F1 as needed).
