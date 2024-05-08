---
marp: true
# dark theme
class: invert
---
<!-- headingDivider: 1 -->

# About this contents

GitHub Pages: [https://inoue-ryoki.github.io/cloudformation/](https://inoue-ryoki.github.io/cloudformation/)

Repository: [https://github.com/inoue-ryoki/cloudformation - GitHub](https://github.com/inoue-ryoki/cloudformation)

# CloudFormationについて

CloudFormationとはAWSが提供している、インフラをコードで定義し管理ができるサービス。

マネジメントコンソールから手動で作成しなくても、コードを走らせると自動で作成される。

なので一度消して→再構築するのもすぐにできる

# 手動で作成するもの

- Secret Managerにmaster.keyを入れて置いた
- IAMのポリシー
- IAMのロール
- Route53

# Secret Managerにmaster.keyを登録

①Railsアプリのmaster.keyの値を確認

②AWS Secrets Managerから新しいシークレットを保存する

③その他のシークレットタイプを選んで、プレーンテキストの中にmaster.keyの値を入れる

# IAMのポリシー作成

①IAMからポリシーを選択

②ポリシーの作成

③JSONで以下のように入れて登録

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

# IAMのロールを作成

①IAMからロールを選択 ロールの作成

②ユースケースではEC2を選択(だった気がする。。)

③許可ポリシーに、AmazonEC2ReadOnlyAccess、AmazonSSMManagedInstanceCore、②で作成したポリシーを追加

# コード書く前に準備

- AWS CLIをインストール
- .ssh/config等を編集してAWSにログインできるように
