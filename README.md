---
marp: true
# dark theme
class: invert
---

<!-- headingDivider: 1 -->

# About this contents

GitHub Pages: [https://inoue-ryoki.github.io/cloudformation/](https://inoue-ryoki.github.io/cloudformation/)

Repository: [https://github.com/inoue-ryoki/cloudformation - GitHub](https://github.com/inoue-ryoki/cloudformation)

# CloudFormation について

CloudFormation とは AWS が提供している、インフラをコードで定義し管理ができるサービス。

マネジメントコンソールから手動で作成しなくても、コードを走らせると自動で作成される。

なので一度消して → 再構築するのもすぐにできる

# 手動で作成するもの

- Secret Manager に master.key を入れて置いた
- IAM のポリシー
- IAM のロール
- Route53

# Secret Manager に master.key を登録

①Rails アプリの master.key の値を確認

②AWS Secrets Manager から新しいシークレットを保存する

③ その他のシークレットタイプを選んで、プレーンテキストの中に master.key の値を入れる

# IAM のポリシー作成

①IAM からポリシーを選択

② ポリシーの作成

③JSON で以下のように入れて登録

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "secretsmanager:GetSecretValue",
            "Resource": [
                "(secretmanagerのARNを入れる)"
            ]
        }
    ]
}
```

# IAM のロールを作成

①IAM からロールを選択 ロールの作成

② ユースケースでは EC2 を選択(だった気がする。。)

③ 許可ポリシーに、AmazonEC2ReadOnlyAccess、AmazonSSMManagedInstanceCore、② で作成したポリシーを追加

# コード書く前に準備

- AWS CLI をインストール
- .ssh/config 等を編集して AWS にログインできるように

# AWS CLI をインストール

linux の環境なら以下のコマンド

```
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install

```

# .ssh/config 等を編集して AWS にログインできるように

※sudo の方には identityfile が必要。シークレットマネージャーの中に入っているのでそれをコピーして secret ディレクトリの中に格納しておく。
※--target の後ろは ec2 インスタンスの id を入れる。 AWS のコンソールから EC2→ インスタンスで確認できる

```
host app-stg-sudo
  user ubuntu
  identityfile ~/secret/app-stg.pem
  proxycommand aws ssm start-session --target [ec2インスタンスのid] --document-name AWS-StartSSHSession
  serveraliveinterval 15
host app-stg
  user u
  proxycommand aws ssm start-session --target [ec2インスタンスのid] --document-name AWS-StartSSHSession
  serveraliveinterval 15
```

# templete.yml の中に CloudFormation のコードを書いていく（ここから本題）

準備ができたのでコードを書いていく。

リファレンス
https://docs.aws.amazon.com/ja_jp/AWSCloudFormation/latest/UserGuide/template-anatomy.html
