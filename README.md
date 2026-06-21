# Skin Cancer Classification

This project implements a deep learning model to classify dermatoscopic images of skin lesions into seven distinct categories.
Since melanoma is a malignant tumor, failing to correctly identify a melanoma case (false negative) can have serious medical consequences. Therefore, ***recall*** serves as the primary evaluation metric, with a specific focus on maximizing ***melanoma recall***.

The dataset exhibits a severe class imbalance, containing a high volume of benign samples and relatively few malignant cases, particularly melanoma. To address this issue, five different imbalance-handling strategies were evaluated:

- Square-root scaled inverse-frequency weighted loss + weighted random sampler
- Weighted random sampler only
- Focal loss only
- Inverse-frequency weighted loss only
- Square-root scaled inverse-frequency weighted loss only

For each method, the optimal checkpoint was selected based on melanoma recall within validation set, reflecting the clinical priority of minimizing false negatives for melanoma.

The resulting models were then compared across standard classification performance metrics. Among the evaluated approaches, the inverse-frequency weighted loss model achieved the optimal balance among melanoma recall, macro F1-score, and classification performance. Therefore, it was selected as the final model for this project.

The final model utilizes a weighted cross-entropy loss function with inverse-frequency class weights. This approach applies large penalties to rare classes and helps the model better identify malignant lesions while maintaining strong performance.


All project files are available in the following folder:
[Project folder](https://drive.google.com/drive/folders/1Qv6buAefBZnGuBYxycTklmDHE6uPNURm?usp=share_link) 

---


## Dataset
The dataset used in this project is HAM10000 (Skin Cancer MNIST), originally introduced in the ISIC 2018 Challenge.
- Original Challenge: https://challenge.isic-archive.com/landing/2018/
- Dataset: [HAM10000 ("Human Against Machine with 10000 training images")-Skin Cancer MNIST in Kaggle
 ](https://www.kaggle.com/datasets/kmader/skin-cancer-mnist-ham10000)


The dataset consists of 7 skin lesion classes with significant class imbalance, containing approximately 10,015 images in total.
- Class distribution:
 
| Class Code | Lesion Type | Image Count | Percentage |
| :--- | :--- | :--- | :--- |
| **nv** | Melanocytic nevi (Benign) | 6,705 | ~66.9% |
| **mel** | **Melanoma (Malignant)** | **1,113** | **~11.1%** |
| **bkl** | Benign keratosis-like lesions | 1,099 | ~11.0% |
| **bcc** | Basal cell carcinoma (Malignant) | 514 | ~5.1% |
| **akiec** | Actinic keratoses / Bowen’s disease | 327 | ~3.3% |
| **vasc** | Vascular lesions | 142 | ~1.4% |
| **df** | Dermatofibroma | 115 | ~1.1% |    
    
This imbalance highlights the challenge of training a robust classifier, particularly for minority classes.




<img width="704" height="393" alt="image" src="https://github.com/user-attachments/assets/35336533-d17b-46fb-8f19-8ed7b1d001bc" />



## Train / Validation / Test split
The original challenge provides only training data, while the test set remains private. In this project, we instead use the dataset available on Kaggle and split it into training set 70%, validation set 15% and test set 15%.

**Data Leakage Prevention:**
To prevent data leakage, the split is performed at the lesion level, ensuring that images from the same lesion do not appear across different subsets.

---

## Data Preprocessing and Augmentation

All images were resized to 224×224 pixels to match the input size required by the model. ImageNet normalization was applied to align the input distribution with the pretrained weights of ResNet18.

To improve generalization, data augmentation was applied only to the training set:

- Random horizontal flip  
- Random rotation (±10 degrees)  

No augmentation was applied to validation and test sets in order to ensure a fair and consistent evaluation.

---

## Model

A pretrained ResNet18 model was used as the backbone for image classification. The final fully connected layer was replaced with a 7-class output layer corresponding to the skin lesion categories.

ResNet18 was chosen because this task requires image classification only (not detection or segmentation), and because it provides a good balance between performance and computational efficiency. This allows training on a resource-constrained environments.

---

## Training Strategy

All models were trained using the same pipeline:

- Optimizer: Adam  
- Learning rate: 1e-4  
- Scheduler: StepLR (step size = 5, gamma = 0.1)  
- Batch size: 32  
- Epochs: 20  

The main evaluation metric during training was **melanoma recall on the validation set**, since false negatives are particularly critical in medical diagnosis.

For each method, the best model checkpoint was selected based on validation melanoma recall.

---

## Handling Class Imbalance

The dataset is highly imbalanced, with a large number of benign cases (melanocytic nevi) and relatively few melanoma cases.

This imbalance can bias the model toward the majority class, significantly affecting its performance on minority classes like melanoma.
Since the main objective of this project is to improve melanoma classification performance, different imbalance-handling strategies were systematically evaluated.

The following five approaches were compared:

- [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/1zXJYapUXX3lupdN_ye3Z4Va38i7fkHNW) **Inverse-frequency weighted loss**
  
  Class weights are computed as the inverse of class frequencies, assigning higher penalties to rare classes.

- [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/1-KhG4GrDcbr7kfFabSneE8WE0Z4JtXCw) **Square-root scaled inverse-frequency weighted loss**

  A softened version of inverse-frequency weighting, where class weights are reduced using a square-root transformation to prevent overly large gradients from rare classes.

- [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/16k8jmtHa-0sGwH68GikQrgFcKEz-JPWd) **Weighted random sampler only**
  
  The training data distribution is balanced by oversampling minority classes without modifying the loss function.

- [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/1hEp8_8HEQ0FnqvrAUpbkGxf3Qmg95EzV) **Focal loss only**
  
  A loss function that down-weights easy examples and focuses training on hard-to-classify samples.

- [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/1se8BkdNwPSkIA6nERS8Wg8jLxxATM1qy) **Square-root scaled inverse-frequency weighted loss + weighted random sampler**
 
  A combination of softened class weighting and balanced sampling to address both loss-level and data-level imbalance.


These strategies compare different approaches to handling class imbalance, including loss-based methods, sampling-based methods, and their combination.

---

## Results

All models were evaluated using overall accuracy, macro F1-score, and melanoma recall.

| Strategy | Accuracy | Macro F1 | Melanoma Recall |
| :--- | :---: | :---: | :---: |
| *Hybrid (Sq-root + Sampler)* | 0.6669 | 0.62 | **0.6601** |
| **Inverse-frequency weighted loss (Selected)** | **0.8126** | **0.75** | **0.6405** |
| *Weighted random sampler only* | 0.6424 | 0.59 | **0.6013** |
| *Square-root scaled inverse-frequency weighted loss* | 0.8397 | 0.73 | **0.5817** |
| *Focal loss only* | 0.8099 | 0.64 | **0.5621** |


The results show that class imbalance handling has a significant impact on performance in medical image classification.

*   **Weighted Random Sampling:**
    Assisted in improving class balance during training, but did not reliably improve melanoma recall, suggesting that sampling alone is not sufficient for this problem. However, when combined with square-root scaled inverse-frequency weighting, a partial improvement in melanoma recall was observed.
*   **Focal Loss:**
    Focused learning on hard examples; however, it did not perform better than weighted cross-entropy in this dataset, likely due to the strong class imbalance.
*   **Square-Root Scaled Inverse-Frequency Weighting:**
    Reduced the effect of extreme class imbalance but simultaneously weakened the model’s sensitivity to rare classes such as melanoma.

### Conclusion
In summary, these results indicate a trade-off between enhanced performance on minority classes and maintaining stable overall classification performance. Among the evaluated methods, inverse-frequency weighted cross-entropy provided the most balanced performance, suggesting that directly using class frequency in the loss function is an effective strategy for this dataset.

---
### Acknowledgements
Parts of the code and explanations in this project were created with the assistance of ChatGPT by OpenAI.

