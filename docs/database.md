# テーブル設計
テーブルは第３正規化以上を行うこと。
ただし、パフォーマンステストを行った上でパフォーマンスに問題がある場合、
正規化を崩してもよい。

# カラム定義
必ず主キー、もしくは、複合キーを設定すること。

できる限り、Not Null制約を付与すること。
できる限り、初期値を設定し、
数値は0　文字列は空文字とすること。
ただし、理由がある場合は、順守しなくともよい。
