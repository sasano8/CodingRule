# Integration Suite

# Python
Pythonを中心に統一したい場合のシステム
- [参考](https://ja.wikipedia.org/wiki/Python%E3%82%92%E4%BD%BF%E3%81%A3%E3%81%A6%E3%81%84%E3%82%8B%E8%A3%BD%E5%93%81%E3%81%82%E3%82%8B%E3%81%84%E3%81%AF%E3%82%BD%E3%83%95%E3%83%88%E3%82%A6%E3%82%A7%E3%82%A2%E3%81%AE%E4%B8%80%E8%A6%A7)

## Development TOol
- Visual Studio Code
- Anaconda
- Pycharm


## ETL
- Airflow

## Business Process Management
- Viewflow
- SpiffWorkflow

## Web Framework
- Django
- flask

## Documentation
- Sphinx
- readthedocs
- reStructuredText
- MkDocs(おすすめ)
- AsciiDoc
- summary-to-pptx(powerpointを作成)
- CDK(Twitter製。AsciiDocをHTMLスライドに変換)
- SlideShare To PDF
- Pyagram（状態遷移図出力ツール）
- NoTex.ch(オンライネディタ）
- PryNotes
- Booktype(オンライン電子書籍作成ツール)

## Scraping/crawler/Enterprise Application Integration
- scrapy(web crawler)
- fscrawler(file system crawler)
- Apache ManifoldCF(file system crawler)
- oneWEX(有償 IBM製 file system crawler）
- PasteHunter(キーワードを設定しモニタする)
- Talend
- DataSpider(有償）


## Infrastracture as a code
- Ansible

## Test Tool
- Dogtail(GUI TEST TOOL デスクトップなど操作できるから色々悪さできる？）

## Colabolation
- MoinMoin(Wiki Engine)
- Plone(Headless CMS)
- Wagtail(CMS)
- imgpush(画像共有サーバー)
- Photonix(機械学習画像管理)
- zulip(slack風チャット)
- Reminiscence(ブックマークサーバ)
- Waliki(wiki)
- Allura(プロジェクト管理)
- Mezzanine
- Askbot(QAシステム）
- yummy（Deliciousクローン）


## Data Analysis
- Pandas
- scikit-learn(機械学習)
- Matplotlib(グラフ生成ライブラリ)
- NumPy(巨大な多次元配列や行列を扱うライブラリ)
- Maltego(ExtensionがPythonに対応)
- Apache Superset(Caravel Airbnb製)
- brat(annotation)
- prodigy(annotation)
- doccano(annotation)
- apache tika(コンテンツの検出と分析)


## Other
- Jinja(Template Engine)
- Python Shell(Google Chromeで動作するPython）
- ERP5(ERP)
- graph-cli(CSVを画像化）
- xltrail(VBA差分出力ツール)
- xlwings(ExcelのAPIをPythonで利用する)
- SQLAlchemy(ORM)

# 外部システム

## OCR
- Tegaki
- AIよみとーる



# Frontend

## grid
- [ag-grid](https://www.ag-grid.com/example.php#/)
- handsontable

やりたいこと
仮想Iaasの構築

コマンドを実行すると仮想Iaasのコマンドラインモードになる。
infra-init      '仮想Iaasの作成
  Sqliteのデータベースを作成

infra-builder   '仮想Iaasの構築モード(CUI)
infra-deploy_web    'Webコンソールを提供するウェブ環境を構築
infra-export_azure
infra-export_aws

add-router_template   template_name   '
add-machine_template   template_name    '
add-router    host_name     'ルータを追加する
add-machine   host_name     'サーバを追加する
add-storage   storage_name  'ストレージを追加する
add-middle    middle_name   '管理するミドルを追加する
add-security_policy         'セキュリティポリシーを追加する
ping          host_name     'カレントホストよりピングを実行する
expot-data      path          '設計したデータを書き出す Jsonかな
ch                          'カレントホストを変更する

コマンドラインモードで実行したコマンドを履歴にとることによって
それ自体が手順になる。
エクスポートにより、よりクリーンな手順を出力可能。


仮想的なインフラ空間を構築し、仮想的な構築を行うことができる。

仮想的なネットワークの疎通テストが可能
仮想的なクラスタの構築
仮想的な負荷テスト

Iassの相性を絞る
Azure Aws
MicorosoftかAmazonに売り込みたい
export-xxxでAzureを構築できるようなコマンドを吐

# TODO
Azure Awsの機能のインフラ思想の理解


