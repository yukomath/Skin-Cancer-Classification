# Skin Cancer Classification

This project aims to build a deep learning model for classifying dermatoscopic images of skin lesions into seven categories.
Since melanoma is a malignant tumor, missing a melanoma case (false negative) can have serious medical consequences. Therefore, ***recall*** is used as the primary evaluation metric, with a particular focus on ***melanoma recall***.

The dataset is highly imbalanced, with a large number of benign samples and relatively few malignant cases, particularly melanoma. To handle this issue, five different imbalance-handling strategies were evaluated:

- Square-root weighted loss + weighted random sampler
- Weighted random sampler only
- Focal loss only
- Inverse-frequency weighted loss only
- Square-root weighted loss only

For each method, the best checkpoint was selected based on melanoma recall on the validation set, reflecting the clinical importance of minimizing false negatives for melanoma.

The resulting models were then compared using classification performance metrics. Among the evaluated approaches, the inverse-frequency weighted loss model achieved the best balance between melanoma recall, macro F1-score, and classification performance. Therefore, it was chosen as the final model for this project.

The final model uses a weighted cross-entropy loss with inverse-frequency class weights, giving larger penalties to rare classes and helping the model better recognize malignant lesions while maintaining strong performance.


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
  - nv (Melanocytic nevi): 6,705
  - mel (Melanoma): 1,113
  - bkl (Benign keratosis-like lesions): 1,099
  - bcc (Basal cell carcinoma): 514
  - akiec (Actinic keratoses / Bowen’s disease): 327
  - vasc (Vascular lesions): 142
  - df (Dermatofibroma): 115
    
This imbalance highlights the challenge of training a robust classifier, particularly for minority classes.




<img width="704" height="393" alt="image" src="https://github.com/user-attachments/assets/35336533-d17b-46fb-8f19-8ed7b1d001bc" />



## Train / Validation / Test split
The original challenge provides only training data, while the test set remains private. In this project, we instead use the dataset available on Kaggle and split it into training set 70%, validation set 15% and test set 15%.

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

ResNet18 was chosen because this task requires image classification only (not detection or segmentation), and because it provides a good balance between performance and computational efficiency. This allows training on a limited computing environment such as Google Colab.

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

This imbalance can negatively affect model performance, especially for rare classes such as melanoma. Since the main objective of this project is to improve melanoma classification performance, different imbalance-handling strategies were systematically evaluated.

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

These strategies were used to compare different approaches to handling class imbalance, including loss-based methods, sampling-based methods, and their combination.

---

## Results

All models were evaluated using overall accuracy, macro F1-score, and melanoma recall.

| Method | Accuracy | Macro F1 | Melanoma Recall |
|--------|----------|----------|------------------|
| Weighted loss only | 0.8126 | 0.75 | 0.6405 |
| Weak weighted loss + sampler | 0.6669 | 0.62 | **0.6601** |
| Only sampler | 0.6424 | 0.59 | 0.6013 |
| Focal loss only | 0.8099 | 0.64 | 0.5621 |
| Weak weighted loss only | **0.8397** | 0.73 | 0.5817 |

Among all methods, the weighted cross-entropy loss achieved the best balance between melanoma recall and overall classification performance.

- [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/1zXJYapUXX3lupdN_ye3Z4Va38i7fkHNW) **Inverse-frequency weighted loss**
  
  Class weights are computed as the inverse of class frequencies, assigning higher penalties to rare classes.

- [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/1-KhG4GrDcbr7kfFabSneE8WE0Z4JtXCw) **Square-root scaled inverse-frequency weighted loss**

  A softened version of inverse-frequency weighting, where class weights are reduced using a square-root transformation to prevent overly large gradients from rare classes.

- [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/16k8jmtHa-0sGwH68GikQrgFcKEz-JPWd) **Weighted random sampler only**
  
  The training data distribution is balanced by oversampling minority classes without modifying the loss function.

- [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/1hEp8_8HEQ0FnqvrAUpbkGxf3Qmg95EzV) **Focal loss only**
  
  A loss function that down-weights easy examples and focuses training on hard-to-classify samples.

- [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/1se8BkdNwPSkIA6nERS8Wg8jLxxATM1qy) **Square-root scaled inverse-frequency weighted loss + weighted random sampler**
 

All models were evaluated using the same test set and the following metrics:

- Overall accuracy  
- Per-class recall  
- Melanoma recall  
- Macro F1-score  
- Confusion matrix  

Among the five approaches, the inverse-frequency weighted loss model achieved the best overall performance. It showed the best balance between melanoma recall, macro F1-score, and overall classification performance.

Therefore, this model was selected as the final model.
---

## Discussion

The results show that class imbalance handling has a significant impact on performance in medical image classification.

Weighted random sampling improved class balance during training but did not consistently improve melanoma recall.

Focal loss improved learning on hard examples but did not outperform weighted cross-entropy in this dataset.

Square-root weighting reduced the effect of extreme imbalance but also weakened sensitivity to rare classes.

In contrast, inverse-frequency weighted loss provided the best trade-off between learning rare classes and maintaining overall performance. This suggests that directly using class frequency in the loss function is an effective strategy for this dataset.

---

## Conclusion

This project evaluated multiple strategies for handling severe class imbalance in skin lesion classification.

Among all methods, inverse-frequency weighted cross-entropy loss achieved the best performance and was selected as the final model.

This approach improved melanoma detection performance while maintaining strong overall classification results.

These results highlight the importance of loss function design in medical image classification tasks with highly imbalanced datasets.
