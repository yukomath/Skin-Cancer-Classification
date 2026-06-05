# Skin Cancer Classification

This project aims to build a deep learning model for classifying dermatoscopic images of skin lesions into seven categories.
Since melanoma is a malignant tumor, missing a melanoma case (false negative) can have serious medical consequences. Therefore, ***recall*** is used as the primary evaluation metric, with a particular focus on ***melanoma recall***.

The dataset is highly imbalanced, with a large number of benign samples and relatively few malignant cases, particularly melanoma. To address this issue, five different imbalance-handling strategies were evaluated:

- Square-root weighted loss + weighted random sampler
- Weighted random sampler only
- Focal loss only
- Inverse-frequency weighted loss only
- Square-root weighted loss only

For each training configuration, the best checkpoint was selected based on melanoma recall on the validation set, reflecting the clinical importance of minimizing false negatives for melanoma.

The resulting models were then compared using overall classification performance metrics. Among the evaluated approaches, the inverse-frequency weighted loss model achieved the best overall balance between melanoma recall, macro F1-score, and overall classification performance. Therefore, it was chosen as the final model for this project.

The final model uses a weighted cross-entropy loss with inverse-frequency class weights, assigning larger penalties to underrepresented classes and encouraging the model to better recognize malignant lesions while maintaining strong overall performance.

All project files are available in the following folder:
[Project folder](https://drive.google.com/drive/folders/1Qv6buAefBZnGuBYxycTklmDHE6uPNURm?usp=share_link) 


## Dataset
The dataset used in this project is HAM10000 (Skin Cancer MNIST), originally introduced in the ISIC 2018 Challenge.
- Original Challenge: https://challenge.isic-archive.com/landing/2018/
- Dataset: [HAM10000 ("Human Against Machine with 10000 training images")-Skin Cancer MNIST in Kaggle
 ](https://www.kaggle.com/datasets/kmader/skin-cancer-mnist-ham10000)

### Dataset Overview
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



Number of lesion:

<img width="704" height="393" alt="image" src="https://github.com/user-attachments/assets/35336533-d17b-46fb-8f19-8ed7b1d001bc" />


Explanation how unbalanced
### Train / Split
The original challenge provides only training data, while the test set remains private. In this project, we instead use the dataset available on Kaggle and split it into training set 70%, validation set 15% and test set 15%.

To prevent data leakage, the split is performed at the lesion level, ensuring that images from the same lesion do not appear across different subsets.



データ - Skin Cancer HAM 2000 - Original Challenge is here - 悪性と良性、メラノーマに着目すること - トータルの数と７種類それぞれの数 - アンバランス性の説明 データの分割と処理 - Trainingデータのみ公開されており、testデータは非公開です。本プロジェクトではKaggleからデータを用い、trainデータを、train70％, Evaluation15％, test15％の割合で分割して使います - training data には、 Augumentation などの処理をしています。 

モデル 
## Model
- Architecture: ResNet18 (pretrained on ImageNet)
Final layer modified for 7-class classification
Reason for choosing ResNet18:
Lightweight and efficient
Suitable for limited computational resources

- モデルは、このプロジェクトではResNet-18を使っています。もちろんもっと良いモデルが存在することは理解していますが、軽いモデルなので自分の実行できる環境ではベストかなと思い採用しています。今後もっとより良い環境で実行することができれば、他のモデルも試したいす。 

クラスごとのデータ数アンバランスの扱い 

1. Loss functionと重みの調整 - Loss function はエントロピーロスを使っています。Focal lossでも実行してみましたが、結果は特に良くなりませんでした。 - クラスごとのデータ数アンバランスを扱うために、重みをつけました。重みは少ないかずのクラスのデータを、、、と言うメリットがあります。 - 重みは、最初１でつけて、0.25, 0.4 0.6などを計算してみましたが、クラスごとのrecallのバランスが一番良さそうなのは、0.５（square root）の時なので、ベストモデルは0.5の場合としています。 - 重みの式は、




2. Sampler - サンプラーは、少ない数のクラスのデータを、、、と言うメリットがあります。 - samplerの式は、　　
