やりたいこと
仮想Iaasの構築

コマンドを実行すると仮想Iaasのコマンドラインモードになる。
infra-init      '仮想Iaasの作成
infra-builder   '仮想Iaasの構築モード(CUI)
infra-export_azure
infra-export_aws

add-router_template   template_name   '
add-machine_template   template_name    '
add-router    host_name     'ルータを追加する
add-machine   host_name     'サーバを追加する
add-storage   storage_name  'ストレージを追加する
add-middle    middle_name   '管理するミドルを追加する
add-security_policy         'セキュリティポリシーを追加する
ping          host_name     'ピングを実行する
expot-data      path          '設計したデータを書き出す Jsonかな
ch                          'カレントホストを変更する

コマンドラインモードで実行したコマンドを履歴にとることによって
それ自体が手順になる。

仮想的なインフラ空間を構築し、仮想的な構築を行うことができる。

仮想的なネットワークの疎通テストが可能
仮想的なクラスタの構築
仮想的な負荷テスト

Iassの相性を絞る
Azure
Micorosoftに売り込みたい
export-xxxでAzureを構築できるようなコマンドを吐
