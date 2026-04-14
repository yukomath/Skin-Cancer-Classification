# Skin-Cancer-Classification
Skin Cancer Classification 

All project files are available in the following folder:
[Project folder](https://drive.google.com/drive/folders/1STEpBd5-sPoryYCv_VT3BjC2v0yZ1zlg?usp=share_link) 

本プロジェクトでは、７種類の皮膚の腫瘍の画像データを分類するモデルをディープラーニングの手法を用いて作成しました。メラノーマは悪性なので、見落としを防ぐために、評価指数としてRecallを用いました。 Original Challengeはここ(リンク)で、Trainingデータのみ公開されており、testデータは非公開です。本プロジェクトではKaggleからデータを用い、trainデータを、train70％, Evaluation15％, test15％の割合で分割して使いました。分割はデータリークを防ぐために、lesion数での割合で分割しています。データはクラスごとにアンバランスで、悪性の腫瘍、特にメラノーマはデータ数が少ないです。アンバランスを修正するために、Samplerを用いたのと、 loss functionに重みをつけて評価しています。 

## Overview
This project aims to build a deep learning model for classifying dermatoscopic images of skin lesions into seven categories.
Since melanoma is a malignant tumor, missing a melanoma case (false negative) can have serious medical consequences. Therefore, recall is used as the primary evaluation metric, with a particular focus on melanoma recall.

The original challenge (link) provides only training data, while the test set remains private. In this project, we instead use the dataset available on Kaggle and split it into:

- Training set: 70%
- Validation set: 15%
- Test set: 15%

To prevent data leakage, the split is performed at the lesion level, ensuring that images from the same lesion do not appear across different subsets.

The dataset is highly imbalanced, with a large number of benign samples and relatively few malignant cases, especially melanoma. To address this imbalance, we apply:

- a Weighted Random Sampler to rebalance the training data distribution
- a weighted loss function to emphasize minority classes during training

These techniques help improve the model’s ability to detect melanoma while maintaining overall performance.

## Data
Original Challenge is [here](https://challenge.isic-archive.com/landing/2018/)
Dataset: HAM10000 (Skin Cancer MNIST)
Source: Kaggle (based on the original challenge)
Number of classes: 7


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
