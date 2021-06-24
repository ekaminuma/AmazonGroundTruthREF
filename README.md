# AmazonGroundTruthREF



---
- [Streamlining data labeling for YOLO object detection in Amazon SageMaker Ground Truth](https://awsfeed.com/whats-new/machine-learning/streamlining-data-labeling-for-yolo-object-detection-in-amazon-sagemaker-ground-truth)

  - 1.Setting up your S3 bucket
  - 2.Creating the manifest file
  - 3.Creating the Ground Truth job
  - 4.Specifying the job details
  - 5.Specifying the task type
  - 6.Creating a team of labelers
  - 7.Specifying labeling task details
  - 8.Sign in and start labeling 
  - 9.Checking job status＝ラベル付けが完了すると、ラベル付けジョブのステータスが[完了]に変わります。「output.manifest」という注釈結果を含む新しいJSONファイルが生成されているのを確認できます。
  - 10.Parsing Ground Truth annotations＝parse_annot.pyをサイトからコピペで保存します。AWS CLIから「python parse_annot.py」コマンドを実行します。Ground Truthは、x座標とy座標、およびその高さと幅の4つの数値のバウンディングボックス情報を返します。parse_gt_outputはoutput.manifestファイルをスキャンして、パンダのデータフレーム内の各画像のすべてのバウンディングボックスの情報を保存します。save_df_to_s3はS3バケットに表形式ファイル「annot.csv」を保存します。
  - 11.Generating YOLO annotations 

---

- Amazon SageMaker Ground Truthの[AWSブログ](https://aws.amazon.com/jp/blogs/news/)で[GTタグ情報](https://aws.amazon.com/jp/blogs/news/category/artificial-intelligence/amazon-sagemaker-ground-truth/)
  -  
