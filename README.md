# AmazonGroundTruthREF

- [Amazon SageMaker Ground Truth でラベル付けしたデータセットを使用して、モデルを簡単にトレーニングする](https://aws.amazon.com/jp/blogs/news/easily-train-models-using-datasets-labeled-by-amazon-sagemaker-ground-truth/) 拡張マニフェスト
- [Amazon SageMaker Ground Truth で作成したデータを使用してオブジェクト検出でモデルを作成してみました](https://dev.classmethod.jp/articles/amazon-sagemaker-object-detection-with-ground-truth/)
- [Nyantech 機械学習を使って写真に写っている猫を見分けてみよう！](https://aws.amazon.com/jp/builders-flash/202003/sagemaker-groundtruth-cat/?awsf.filter-name=*all)
  - 前編: Amazon SageMaker Ground Truth を使った画像のラベリング
  - 中編︓[Amazon Rekognition Custom Labels を使った機械学習モデルの作成](https://aws.amazon.com/jp/builders-flash/202004/sagemaker-groundtruth-cat/?awsf.filter-name=*all)
  - 後編︓[Amazon SageMaker を使った機械学習モデルの作成](https://aws.amazon.com/jp/builders-flash/202005/sagemaker-cat/?awsf.filter-name=*all)
---
Amazon SageMaker Ground Truth, [FAQ](https://aws.amazon.com/jp/sagemaker/groundtruth/faqs/), [DeveloperRes](https://aws.amazon.com/jp/sagemaker/groundtruth/developer-resources/)
- 数ステップでAIモデル構築用のデータのラベル付けジョブの作業設定ができるマネージドサービス
- Ground Truth はデータを AWS 環境外に保存したりコピーできない
- Ground Truth は一般データ保護規則 (GDPR) などのコンプライアンス基準をサポート
- Amazon CloudWatch および Amazon CloudTrail でログ記録および監査機能を提供

---
- [Streamlining data labeling for YOLO object detection in Amazon SageMaker Ground Truth](https://awsfeed.com/whats-new/machine-learning/streamlining-data-labeling-for-yolo-object-detection-in-amazon-sagemaker-ground-truth)
YOLO注釈生成まで[目次と解説文抜粋]

  - 1.Setting up your S3 bucket
  - 2.Creating the manifest file
  - 3.Creating the Ground Truth job
  - 4.Specifying the job details
  - 5.Specifying the task type
  - 6.Creating a team of labelers
  - 7.Specifying labeling task details
  - 8.Sign in and start labeling 
  - 9.Checking job status＝ラベル付けが完了すると、ラベル付けジョブのステータスが[完了]に変わります。「output.manifest」という注釈結果を含む新しいJSONファイルが生成されているのを確認できます。
  - 10.Parsing Ground Truth annotations
     - parse_annot.pyとして、サイトからCODEをコピペして保存します。
     - AWS CLIから「python parse_annot.py」コマンドを実行します。Ground Truthは、x座標とy座標、およびその高さと幅の4つの数値のバウンディングボックス情報を返します。parse_gt_outputはoutput.manifestファイルをスキャンして、パンダのデータフレーム内の各画像のすべてのバウンディングボックスの情報を保存します。save_df_to_s3はS3バケットに表形式ファイル「annot.csv」を保存します。
     - 「output.manifest」のJSONファイルから「annot.csv」に変更する理由は、メタデータなど余計な情報がmanifestに含まれているからです。
     - Amazon S3からannot.csvファイルをローカルコピーするには次コマンドを実行します。
     - aws s3 cp s3://ground-truth-data-labeling/bounding_box/ground_truth_annots/yolo-bbox/annot.csv 
     - パンダのDFに読み戻し、最初の数行を調べることができます。
     
  - 11.Generating YOLO annotations 
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
  - 

---
日本のSageMaker活用事例

- 2020-02-27,SONY [SoVeC Smart Videoという、深層学習を活用した動画自動生成クラウドサービスを短期間でリリース](https://aws.amazon.com/jp/blogs/news/aws-aiml-tokyo2/) 動画の素材として利用者から提供された静止画が低解像であっても SRGAN（Super Resolution Generative Adversarial Network）を用いて高精細化,ローカルPCへのSageMaker環境構築方法、Amazon SageMaker Managed Spot Training利用の注意点.[Slides](https://pages.awscloud.com/rs/112-TZM-766/images/3_AWS_AI_et_ML_at_Tokyo_No_2_usecase_Sony_Oishi_handout.pdf)
- 
