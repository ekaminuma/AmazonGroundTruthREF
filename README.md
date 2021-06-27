# Amazon SageMaker Ground Truth(SageMakerGT)

---
- [Amazon SageMaker Ground Truth でラベル付けしたデータセットを使用して、モデルを簡単にトレーニングする](https://aws.amazon.com/jp/blogs/news/easily-train-models-using-datasets-labeled-by-amazon-sagemaker-ground-truth/) 
    - ラベル付きデータセット=入力データセットオブジェクトを、メタデータを使用してインライン展開する拡張マニフェストファイル形式で作成される。
    - [1] 以前は、拡張されたデータセットでModelをTrainingするために、低レベルの AWS SDK API しか使用できませんでした。
    - [2] Amazon SageMakerコンソールで数回クリック[2-1]するか、Amazon SageMaker Python SDK を使用して1行の API Call[2-2]でTrainingをすばやく簡単に実行できるようになりました(2018年12月)。
    - [3] Amazon SageMakerのPipe modeを使用してTrainingすることができます。このモードはS3から Amazon SageMaker にデータがストリーミングされる速度を大幅に高速化するので、トレーニングジョブが素早く完了し、全体的なコストを削減できます。
- [Augmented Manifest(拡張マニフェスト)ファイルの説明](https://docs.aws.amazon.com/sagemaker/latest/dg/augmented-manifest.html)
   - ラベル付きデータセットは、拡張マニフェストファイル形式で作成される。 
   - 拡張マニフェストファイルはJSON形式。

---
- [Amazon SageMaker Ground Truth で作成したデータを使用してオブジェクト検出でモデルを作成してみました](https://dev.classmethod.jp/articles/amazon-sagemaker-object-detection-with-ground-truth/)
    - SageMaker [[ipynb]](https://gist.github.com/furuya02/1429744465506d6080813cafc8fe9579) 
- [Nyantech 機械学習を使って写真に写っている猫を見分けてみよう！](https://aws.amazon.com/jp/builders-flash/202003/sagemaker-groundtruth-cat/?awsf.filter-name=*all) 
  - **Rekognition** or **SageMaker** ＝RecognitionはGUIのみで推論・学習ができる組込み AI サービス（推論だけでない学習時はCustom Labels利用）。一方、SageMakerはプログラミングが必要。 
  - 前編: [Amazon SageMaker Ground Truth を使った画像のラベリング](https://aws.amazon.com/jp/builders-flash/202003/sagemaker-groundtruth-cat/?awsf.filter-name=*all)
  - 中編︓前編の続き[Amazon Rekognition Custom Labels を使った機械学習モデルの作成](https://aws.amazon.com/jp/builders-flash/202004/sagemaker-groundtruth-cat/?awsf.filter-name=*all) **アルゴリズム選択不可＝BlackBoxモデル**
  - 後編︓前編の続き[Amazon SageMaker を使った機械学習モデルの作成](https://aws.amazon.com/jp/builders-flash/202005/sagemaker-cat/?awsf.filter-name=*all) **アルゴリズム選択可**
 
---
Amazon SageMaker Ground Truth (SageMakerGT), [FAQ](https://aws.amazon.com/jp/sagemaker/groundtruth/faqs/), [DeveloperRes](https://aws.amazon.com/jp/sagemaker/groundtruth/developer-resources/)
   - SageMakerGTは、数ステップでAIモデル構築用のデータラベリング作業環境を作成できるマネージドサービス
   - Ground Truth はデータを AWS 環境外に保存したりコピーできない
   - Ground Truth は一般データ保護規則 (GDPR) などのコンプライアンス基準をサポート
   - Amazon CloudWatch および Amazon CloudTrail でログ記録および監査機能を提供

---
SageMakerGT後のYOLO物体検出モデル学習

- [Streamlining data labeling for YOLO object detection in Amazon SageMaker Ground Truth](https://awsfeed.com/whats-new/machine-learning/streamlining-data-labeling-for-yolo-object-detection-in-amazon-sagemaker-ground-truth)

[目次と解説文抜粋]

  - **1.Setting up your S3 bucket**/ **2.Creating the manifest file**/ **3.Creating the Ground Truth job** / **4.Specifying the job details** / **5.Specifying the task type** / **6.Creating a team of labelers** / **7.Specifying labeling task details**/  **8.Sign in and start labeling** 
  - **9.Checking job status**＝ラベル付けが完了でステータスが[完了]に。「output.manifest」という注釈結果を含むJSONファイルが生成。
  - **10.Parsing Ground Truth annotations**　[annotationの形式変換[1] output.manifest-->csv-->pandas DF]
     - parse_annot.pyとして、サイトからCODEをコピペして保存します。
     - AWS CLIから「python parse_annot.py」コマンドを実行します。Ground Truthは、x座標とy座標、およびその高さと幅の4つの数値のバウンディングボックス情報を返します。parse_gt_outputはoutput.manifestファイルをスキャンして、Pandasのデータフレーム内の各画像のすべてのバウンディングボックスの情報を保存します。save_df_to_s3はS3バケットに表形式ファイル「annot.csv」を保存します。
     - 「output.manifest」のJSONファイルから「annot.csv」に変更する理由は、メタデータなど余計な情報がmanifestに含まれているからです。
     - Amazon S3からannot.csvファイルをローカルコピーするには次コマンドを実行します。
     - aws s3 cp s3://ground-truth-data-labeling/bounding_box/ground_truth_annots/yolo-bbox/annot.csv 
  - **11.Generating YOLO annotations** [annotationの形式変換[2] GT形式からYOLO形式に変更]
     - YOLO形式では、各境界ボックスは、ボックスの中心座標とその幅と高さによって記述されます。各数値は、画像のサイズに応じて拡大縮小されます。したがって、それらはすべて0から1の範囲です。カテゴリ名の代わりに、YOLOモデルは対応する整数カテゴリを想定しています。
     - データフレームのカテゴリ列の各名前を一意の整数にマップする必要があります。さらに、YOLOv3の公式のDarknet実装では、画像の名前を注釈テキストファイルの名前と一致させる必要があります。たとえば、画像ファイルがのpic01.jpg場合、対応する注釈ファイルにはpic01.txt。という名前を付ける必要があります。
     - annot_yoloプロシージャは、ボックス座標を画像サイズで再スケーリングして作成したデータフレームを変換し、save_annots_to_s3各画像に対応する注釈をテキストファイルに保存してAmazonS3に保存します。
     - これで、いくつかの画像とそれに対応する注釈を調べて、モデルトレーニング用に適切にフォーマットされていることを確認できます。ただし、最初に、画像にYOLO形式のバウンディングボックスを描画する手順を作成する必要があります。
     - AmazonS3から画像と対応する注釈ファイルをダウンロードします。
     - 各バウンディングボックスの正しいラベルを表示するには、辞書でラベルを付けたオブジェクトの名前を指定します。このユースケースでは、ラベルの順序は重要です。**GroundTruthラベリングジョブの作成時に使用した順序と一致している必要があります**。

---
- [FIELD NOTES: GAINING INSIGHTS INTO LABELING JOBS FOR MACHINE LEARNING](https://noise.getoto.net/tag/amazon-sagemaker-ground-truth/)
  - GroundTruthの注釈づけをしたMTurkのラベラーの評価。作業者毎の作業数や、日別統計など。 

---

- Amazon SageMaker Ground Truthの[AWSブログ](https://aws.amazon.com/jp/blogs/news/)で[GTタグ情報](https://aws.amazon.com/jp/blogs/news/category/artificial-intelligence/amazon-sagemaker-ground-truth/)
  - [Amazon SageMaker Ground Truthを利用した動画ラベリングとAmazon Rekognition Custom Labelsへのインポート ](https://aws.amazon.com/jp/blogs/news/amazon-sagemaker-gt-video/)
  - [Amazon Rekognition Custom Labels で特定の動作を認識する機械学習モデルを作る](https://aws.amazon.com/jp/blogs/news/amazon-rekognition-custom-labels-motion-detect/)

---
日本のSageMaker活用事例

[AI/ML@Tokyo](https://aws.amazon.com/jp/blogs/news/tag/ai-mltokyo/)AWSブログより

- 2020-04-26 三菱UFJトラスト投資工学研究所（MTEC） [[Amazon SageMaker を活用した実践・金融データサイエンス]](https://aws.amazon.com/jp/blogs/news/aws-aiml-tokyo10/) [[Slides]](https://pages.awscloud.com/rs/112-TZM-766/images/20210408_AIML_Tokyo_MTEC.pdf)
  - Amazon SageMaker, 国間ニュースは旧予測モデル, 為替ポラティリティ予測モデル,(本)実践金融データサイエンス 
- 2020-04-26 DXYZ株式会社(ディクシーズ):[[AWSを利用した顔認証IDプラットフォーム構築と Amazon Rekognition 活用事例]](https://aws.amazon.com/jp/blogs/news/aws-aiml-tokyo10/)[[Slides]](https://pages.awscloud.com/rs/112-TZM-766/images/3.aimltokyo10_DXYZ.pdf)
  - Amazon Rekognition + AWS Lamba + AWS CloudWatch
- 2020-12-22 東日本旅客鉄道株式会社:[[画像認識を活用した PoC 環境構築事例]](https://aws.amazon.com/jp/blogs/news/aws-aiml-tokyo9/)[[Slides]](https://pages.awscloud.com/rs/112-TZM-766/images/AIML_Tokyo_9_JR_East.pdf)
  - AWS Lamba, 列車が走行する線路におけるレール・ガードレール間の移動量の推定 
- 2020-12-08 株式会社JALインフォテック:[[WebカメラとAmazon Rekognitionを用いた空港混雑度の可視化事例]](https://pages.awscloud.com/rs/112-TZM-766/images/3_AWS_AIML_Tokyo8_JIT.pdf)
  - Amazon Rekognition + AWS Lambda + Amazon Forecast 
  - 羽田空港、人が多すぎて塊で検出、RekognitionがBlackboxなのでパラメータチューニング大変
- 2020-12-08 AWS:[[金融サービスにおける機械学習のベストプラクティス]](https://d1.awsstatic.com/whitepapers/ja_JP/machine-learning-in-financial-services-on-aws.pdf)
- 2020-10-30 株式会社コナミデジタルエンタテインメント:[[『遊戯王ニューロン』における10000種類カード認識大規模データの学習パイプライン]](https://aws.amazon.com/jp/blogs/news/aws-aiml-tokyo7/)[[Slides]](https://pages.awscloud.com/rs/112-TZM-766/images/KONAMI_AIMLTokyo7.pdf)
  - Amazon SageMaker + Airflow  
- 2020-10-30 ヤフー株式会社:[[ハイブリッドMLパイプラインによるビジネスアジリティの向上]](https://aws.amazon.com/jp/blogs/news/aws-aiml-tokyo7/) [[Slides]](https://pages.awscloud.com/rs/112-TZM-766/images/Yahoo_AIMLTokyo7.pdf)
  - Amazon SageMaker + Airflow   
- 2020-09 AWS:[[Amazon SageMakerを使ってAutoGluon-Tabularを活用する方法]](https://aws.amazon.com/jp/blogs/news/aws-aiml-tokyo6/) [[Slides]](https://pages.awscloud.com/rs/112-TZM-766/images/2.AutoGluon-Tabular_SageMaker.pdf)
- 2020-07-27 電通デジタル:[[SageMakerとAirflowによる機械学習モデルの運用自動化について]](https://aws.amazon.com/jp/blogs/news/%E3%80%90%E9%96%8B%E5%82%AC%E5%A0%B1%E5%91%8A%E3%80%91aws-ai-mltokyo-5/)[[Slides]](https://speakerdeck.com/dentsudigital/sagemakertoairflowniyoruji-jie-xue-xi-moterufalseyun-yong-zi-dong-hua-nituite)
  - Amazon SageMaker (XGBoost) + Airflow 
- 2020-06-26 BASE:[[BASEでのAWS Personalizeを活用したレコメンドシステム構築事例]](https://aws.amazon.com/jp/blogs/news/aws-aiml-tokyo4/) [[Slides]](https://pages.awscloud.com/rs/112-TZM-766/images/3.BASE_aws_aiml_tokyo_4_base.pdf)
- 2020-05-19 NTTドコモ:[[マイマガジンサービスでのSageMakerを活用したレコメンドシステム構築事例]](https://aws.amazon.com/jp/blogs/news/aws-aiml-tokyo3/) [[Slides]](https://pages.awscloud.com/rs/112-TZM-766/images/04232020_AI_ML_Tokyo_docomo_handsout.pdf)
  - Amazon SageMaker Autopilot ＋ Studio 
- 2020-02-27,SONY: [[SoVeC Smart Videoという、深層学習を活用した動画自動生成クラウドサービスを短期間でリリース]](https://aws.amazon.com/jp/blogs/news/aws-aiml-tokyo2/) [[Slides]](https://pages.awscloud.com/rs/112-TZM-766/images/3_AWS_AI_et_ML_at_Tokyo_No_2_usecase_Sony_Oishi_handout.pdf)
  - Amazon SageMaker, Amazon SageMaker Managed Spot Training
  - SRGAN（Super Resolution Generative Adversarial Network）を用いて高精細化,ローカルPCへのSageMaker環境構築方法、Spot Trainingの注意点.

---
- AWS CLI(Command Line Interface)
 - [Accessing S3 Bucket From Google Colab](https://medium.com/@lily_su/accessing-s3-bucket-from-google-colab-16f7ee6c5b51)  
 - [How to load data from AWS S3 into Google Colab](https://python.plainenglish.io/how-to-load-data-from-aws-s3-into-google-colab-7e76fbf534d2)
