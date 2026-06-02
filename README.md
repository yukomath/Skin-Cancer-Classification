# Skin Cancer Classification

All project files are available in the following folder:
[Project folder](https://drive.google.com/drive/folders/1STEpBd5-sPoryYCv_VT3BjC2v0yZ1zlg?usp=share_link) 

## Overview
This project aims to build a deep learning model for classifying dermatoscopic images of skin lesions into seven categories.
Since melanoma is a malignant tumor, missing a melanoma case (false negative) can have serious medical consequences. Therefore, ***recall*** is used as the primary evaluation metric, with a particular focus on ***melanoma recall***.

[The original challenge](https://challenge.isic-archive.com/landing/2018/) provides only training data, while the test set remains private. In this project, we instead use the dataset available on [Kaggle](https://www.kaggle.com/datasets/kmader/skin-cancer-mnist-ham10000) and split it into training set 70%, validation set 15% and test set 15%.

To prevent data leakage, the split is performed at the lesion level, ensuring that images from the same lesion do not appear across different subsets.

The dataset is highly imbalanced, with a large number of benign samples and relatively few malignant cases, especially melanoma. To address this imbalance, we apply:

- a ***weighted random sampler*** to rebalance the training data distribution
- a ***weighted loss function*** to emphasize minority classes during training

These techniques help improve the model’s ability to detect melanoma while maintaining overall performance.

## Dataset
The dataset used in this project is HAM10000 (Skin Cancer MNIST), originally introduced in the ISIC 2018 Challenge.
- Original Challenge: https://challenge.isic-archive.com/landing/2018/
- Dataset: [HAM10000 ("Human Against Machine with 10000 training images")-Skin Cancer MNIST in Kaggle
 ](https://www.kaggle.com/datasets/kmader/skin-cancer-mnist-ham10000)

### Dataset Overview
The dataset consists of 7 skin lesion classes with significant class imbalance, containing approximately 10,015 images in total.
Class distribution:
nv (Melanocytic nevi): 6,705
mel (Melanoma): 1,113
bkl (Benign keratosis-like lesions): 1,099
bcc (Basal cell carcinoma): 514
akiec (Actinic keratoses / Bowen’s disease): 327
vasc (Vascular lesions): 142
df (Dermatofibroma): 115
This imbalance highlights the challenge of training a robust classifier, particularly for minority classes.
Original Challenge is [here](https://challenge.isic-archive.com/landing/2018/)

## Detaset
Number of picture:
Number of lesion:

Number of classes: 7
- akiec: Bowen's disease
- bcc: basal cell carcinoma 
- bkl: benign keratosis-like lesions (solar lentigines / 
seborrheic keratoses and lichen-planus like keratoses) 
- df: dermatofibroma 
- mel: melanoma 
- nv: melanocytic nevi 
- vasc: vascular lesions (angiomas, angiokeratomas, pyogenic granulomas and hemorrhage)

Number of image of each class
- dx	
- nv	6705
- mel	1113
- bkl	1099
- bcc	514
- akiec	327
- vasc	142
- df	115

<img width="704" height="393" alt="image" src="https://github.com/user-attachments/assets/35336533-d17b-46fb-8f19-8ed7b1d001bc" />


Explanation how unbalanced

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
