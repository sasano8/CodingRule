# ヘッダ
各ソースファイルの先頭とプロシージャ・ファンクションの上部にヘッダを用意すること。

## ソースヘッダ
```
'ソース名		：
'機能説明		：
'備考		：
'作成者		：2016/12/11
'作成日		：2016/12/11
```

## ファンクションヘッダ
```
'機能名		：
'引数		：	
'戻り値		：Arg1
'			：Arg2'
機能説明		：
'備考		：
'作成者		：2016/12/11
'作成日		：2016/12/11
```

# 型宣言の強制
下記の宣言を行い型の宣言を強制すること。
```
Option Explicit
```
この宣言は以下の設定で自動挿入を行う。

1. 【ツール】→【オプション】
2. 【編集】のタブを選択
3. 【変数の宣言を強制する】をチェックする。

## 理由
変数名のミスがあった場合、コンパイル時にミスを検知することができるため。

# 変数の一括宣言
一行で複数の変数宣言を行う場合、それぞれに型を明示すること。
```
Dim valA as String, valB as Long
```
## 理由
型宣言を行わないと、暗黙の型（Variant）が適用されるため。
```
Dim UserMin, UserMax As Integer   'userMinはVariant型になる。
```

# マクロのスコープ
以下の宣言を利用し、プロシージャ・ファンクションの不要なアクセスレベルを付与しないこと。
```
Option Private Module
```

# 変数のスコープ

# オブジェクトの初期化
以下の正しい例に従い、オブジェクトの初期化を行うこと。
```
'正しい例
Dim obj As MyClass
Set obj = New MyClass

'誤った例
Dim obj As New MyClass 
```

## 理由
オブジェクトの変数宣言と同時に初期化を行うと、コンストラクタの起動タイミングが制御できないため。

# オブジェクトの破棄
使用が終わったオブジェクトは参照を解放すること。
クローズメソッドを持っているオブジェクトはクローズを実施すること。
```
Call obj.Close
Set obj = Nothing
```

また、クラスモジュールを作成時にデストラクタを定義すること。
```
Sub Class_Terminate()
  '処理
End Sub
```

## 理由
オブジェクトは参照カウンタを持っており、参照が0になるとオブジェクトが破棄されるため。
オブジェクトはスコープを抜けると破棄されるが（確認）、
リソース未解放のリスクを軽減するため、明示的に破棄を行うこと。

# 環境依存
参照設定とCreateObjectは動作環境により正常に動作しない可能性があるため、
動作させる可能性がある環境で検証を行うこと。

# 命名規則
意味を略さずに略さずに命名すること。

## クラス名・標準モジュール名
Pascal Case(Upper Camel Case)を利用すること。
```
MyClass
```

## 定数
Upper Case を利用すること。
```
Const START_ROW
```

## 変数
Snake Case を利用すること。
```
Dim ws_test as WorkSheet
```

カウンタなどスコープの狭い変数は「i」「j」などを利用してもよい。
ただし、ネストが3階層以上になるときは、名前に意味を持たせること。

## 日本語名について
業務用件に従った日本語を利用してもよい。
ただし、利用・開発拠点が日本国外である場合は、使用しないこと。

### 理由
用語の乱立を防ぐことができる。
日本国外においては、外国人技術者や動作に支障をきたす可能性がある。

## コントロール名
コンポーネントの種類に応じて、プレフィックスを用いること。

## コールバック
VABでコールバックを利用する場合はCallByNameを利用し、
コールバック対象のオブジェクトは「OnCompleted」を実装すること。
同一クラス・フォームで２つ以上のコールバック関数が必要な場合は、
「OnCompleted + 処理名」とすること。
```
Sub Procedure(callback as Object)
  CallByName callback, "OnCompleted", VbMethod, "おはよー", "おはよー"
End Sub
```

# コメント

# 配列
# ショートサーキット
VBAにおいて、ショートサッキット（短絡評価）は行われないということを認識すること。
```
'IsAの判定がfalseでもIsBが実行される。
if IsA() = "A" And IsB() = "B" then
end if
```

# 3項演算子
VBAの構文に、三項演算子は存在しないが、IIF関数を利用することができる。
ただし、true falseどちらの評価も行われるため、
イミュータブルな値を利用すること。
```
'正しい例
result = IIF(IsTrue(),True,False)

'誤った例
Call IIF(IsTrue(),DoIfTrue(),DoIfFalse()) 'どちらも実行されてしまう。
```

# 日付リテラル
地域により動作が異なるため、日付リテラルを利用すること。
```
Dim birthday as Data
'正しい例
birthday = #12/1/1 12:0:0#
Debug.Print birthday      '日本　2012/1/1 12:00　米国　2001/1/12 12:00

'誤った例
birthday = "12/1/1 12:0:0"
Debug.Print birthday      '
```

## 理由
日付リテラルを利用することで、日付の解釈を固定できるため。


# 型宣言文字
変数や値の末尾にキーワードを付与することで、型を指定することができる。
基本は、変数で型を定義すること。

```
Dim a$ '文字列型
Dim b& '長整数型
Dim c% '整数型
Dim d@ '通貨型
Dim e# '倍精度浮動小数点数型
Dim f! '単精度浮動小数点数型

'または以下のように利用する。
a = 0
a = a$
b = 0&
c = 0%
d = 0@
e = 0#
f = 0!

```



# 引数のキーワードの明示化
参照渡しか値渡しか明示すること。

## 理由
関数の呼び出し方により、値渡し、参照渡しが決定されるため。
```
Dim lVal as long
SubA lVal           '参照渡しになる
SubA(lVal)          '値渡しになる
Call SubA(lVal)     '参照渡しになる
Call SubA((lVal))   '値渡しになる
```

# 設計
モジュール・クラスにまとめる関数の設計は以下を参考とすること。

# エラーハンドリング
VBAにおいて、例外処理はプロシージャ内にエラー処理用のサブルーチンを書く必要がある。
処理によって、適切なエラーハンドリングを行うこと。

## ループ中のエラーハンドリング例
サブルーチンからメインルーチンへ戻るには、Resumeステートメントを利用する。

```
'正しい例（本当か？）
On Error Goto err_handler
for i = 0 to 100 
  '処理
next_loop:
next

resume next_loop

'誤った例（例外発生後は例外を検知できない。）
On Error Goto err_handler
for i = 0 to 100 
  '処理
err_handler:
next

```
## 長いメインルーチンでのエラーハンドリング例
```
処理

OnSccess
	'成功処理
	goto OnComplete

OnError
	'失敗処理
	goto OnComplete

OnComplete
	'クローズ処理
exit function
```

## 繰り返し中のエラーハンドリング例
```
Function ErrHandlingSample()

	On Error Goto err_handler
	
	for each r in Selection

		Call Function01()
		Call Function02()
		Call Function03()
next_loop:
	next
	
	On Error Goto 0

	'無限ループ回避(resumeが無限に走ってしまう。)
	Exit Function

'エラーが発生したら、next_loopへ復帰する
err_handler:
	resume next_loop

End Function
```

# Empty値について
Emptyとは何も値が設定されていない状態であり、
Variant型でのみ有効？である。

Emptyを厳密に区別するためには以下のようなコードで理解を深めるとよい

```
Dim a as Variant
Dim b as Variant

b = 0

'何も値を設定していない状態はIsEmptyでTrueが返る
if a = 0 then debug.print "Not Empty"
if a = Empty then debug.print "Not Empty"
if IsEmpty(a) then debug.print "Empty"

'何か値を設定していればIsEmptyでFalseが返る
if b = 0 then debug.print "Not Empty"
if b = Empty then debug.print "Not Empty"
if IsEmpty(b) then debug.print "Empty"

```
