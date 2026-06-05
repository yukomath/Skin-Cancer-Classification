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

All models were evaluated using the same test set and the following metrics:

- Overall accuracy  
- Per-class recall  
- Melanoma recall  
- Macro F1-score  
- Confusion matrix  

Among the five approaches, the inverse-frequency weighted loss model achieved the best overall performance. It showed the best balance between melanoma recall, macro F1-score, and overall classification performance.

Therefore, this model was selected as the final model.


1. 0.5weak loss and sampler
=== Test Evaluation ===
Overall Accuracy: 0.6669
Melanoma (mel) Recall: 0.6601

Per-class Recall:
  akiec: 0.8704
  bcc: 0.4536
  bkl: 0.6061
  df: 0.6316
  mel: 0.6601
  nv: 0.6793
  vasc: 0.9565

Classification Report:
              precision    recall  f1-score   support

       akiec       0.27      0.87      0.41        54
         bcc       0.67      0.45      0.54        97
         bkl       0.55      0.61      0.58       132
          df       0.92      0.63      0.75        19
         mel       0.29      0.66      0.40       153
          nv       0.96      0.68      0.79      1032
        vasc       0.73      0.96      0.83        23

    accuracy                           0.67      1510
   macro avg       0.63      0.69      0.62      1510
weighted avg       0.81      0.67      0.71      1510

2. Only sampler
=== Test Evaluation ===
Overall Accuracy: 0.6424
Melanoma (mel) Recall: 0.6013

Per-class Recall:
  akiec: 0.6296
  bcc: 0.8351
  bkl: 0.5909
  df: 0.7895
  mel: 0.6013
  nv: 0.6279
  vasc: 0.9565

Classification Report:
              precision    recall  f1-score   support

       akiec       0.35      0.63      0.45        54
         bcc       0.50      0.84      0.63        97
         bkl       0.37      0.59      0.46       132
          df       0.79      0.79      0.79        19
         mel       0.30      0.60      0.40       153
          nv       0.97      0.63      0.76      1032
        vasc       0.52      0.96      0.68        23

    accuracy                           0.64      1510
   macro avg       0.54      0.72      0.59      1510
weighted avg       0.79      0.64      0.68      1510


3. Only focalloss　 
=== Test Evaluation ===
Overall Accuracy: 0.8099
Melanoma (mel) Recall: 0.5621

Per-class Recall:
  akiec: 0.5926
  bcc: 0.5876
  bkl: 0.4545
  df: 0.2632
  mel: 0.5621
  nv: 0.9302
  vasc: 1.0000

Classification Report:
              precision    recall  f1-score   support

       akiec       0.59      0.59      0.59        54
         bcc       0.73      0.59      0.65        97
         bkl       0.73      0.45      0.56       132
          df       0.62      0.26      0.37        19
         mel       0.52      0.56      0.54       153
          nv       0.88      0.93      0.90      1032
        vasc       0.77      1.00      0.87        23

    accuracy                           0.81      1510
   macro avg       0.69      0.63      0.64      1510
weighted avg       0.80      0.81      0.80      1510



4. Only weightedloss
 === Test Evaluation ===
Overall Accuracy: 0.8126
Melanoma (mel) Recall: 0.6405

Per-class Recall:
  akiec: 0.6111
  bcc: 0.8557
  bkl: 0.6970
  df: 0.8947
  mel: 0.6405
  nv: 0.8547
  vasc: 0.9565

Classification Report:
              precision    recall  f1-score   support

       akiec       0.55      0.61      0.58        54
         bcc       0.69      0.86      0.76        97
         bkl       0.63      0.70      0.66       132
          df       0.81      0.89      0.85        19
         mel       0.48      0.64      0.55       153
          nv       0.94      0.85      0.90      1032
        vasc       0.92      0.96      0.94        23

    accuracy                           0.81      1510
   macro avg       0.72      0.79      0.75      1510
weighted avg       0.84      0.81      0.82      1510



5. Only  0.5weakweightedloss

=== Test Evaluation ===
Overall Accuracy: 0.8397
Melanoma (mel) Recall: 0.5817

Per-class Recall:
  akiec: 0.4815
  bcc: 0.7835
  bkl: 0.4318
  df: 0.7895
  mel: 0.5817
  nv: 0.9545
  vasc: 0.8696

Classification Report:
              precision    recall  f1-score   support

       akiec       0.59      0.48      0.53        54
         bcc       0.75      0.78      0.76        97
         bkl       0.88      0.43      0.58       132
          df       0.83      0.79      0.81        19
         mel       0.54      0.58      0.56       153
          nv       0.90      0.95      0.93      1032
        vasc       0.95      0.87      0.91        23

    accuracy                           0.84      1510
   macro avg       0.78      0.70      0.73      1510
weighted avg       0.84      0.84      0.83      1510


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
