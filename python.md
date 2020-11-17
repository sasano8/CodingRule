# 辞書の合成
２つの辞書を合成した場合、重複したキーはどうなるか検証した。
dict(\**dic1, \**dic2)、func(\**dic1, \**dic2)は重複したキーが投入された時、エラーとなるので積極的に使ってよい。
{\**dic1, \**dic2}は、後勝ちで上書きされるので危険。

```
dic1 = {"name": "bob", "age": 20}
dic2 = {"name": "mary"}

# 辞書作成時
{**dic1, **dic2}  # => {"name": "mary", "age": 20}  右辺の値で上書き
dict(**dic1, **dic2)  # => TypeError: func() got multiple values for keyword argument 'name'

# update / 代入演算子
update_dic = {"name": "bob", "age": 20}
update_dic.update(dic2)
# または
update_dic |= dic2
# => {"name": "mary", "age": 20}  右辺の値で上書きされる。また、元データを変更するので、できればあまり使わないほうがよい。

dict(update_dic, **dic2)
# 元のソース自体は更新されない
# => {"name": "mary", "age": 20}

# マージ演算子
new_dic = dict_1 | dict_2
# => {"name": "mary", "age": 20}  右辺の値で上書きされる


# キーワード引数として渡す際に合成
def func(**kwargs):
    print(kwargs)

func(**dic1, **dic2)
# => TypeError: func() got multiple values for keyword argument 'name'
```


# クラス
python3.7未満は、クラスコンストラクタの定義が冗長的。
python3.7以降は、dataclassデコレータでメンバ変数およびに__init__や__str__を自動生成してくれる。
pydanticはdataclassのような機能に加え、様々なオプションを設定でき高機能。
ライブラリ作成時は、依存関係を減らすためdataclassを使いたい。

```
class MyClass:
  def __init__(self, name):
    self.name = name


# python3.7以降
from dataclasses import dataclass

@dataclass
class MyClass:
  name: str


# 外部ライブラリ
from pydantic import BaseModel

class MyClass(BaseModel):
  name: str
```


# 日付の扱い方
日付関連の機能はdatetimeモジュールから利用することができる。
タイムゾーンを操作するには、標準ライブラリだけでは大変で、python-dateutil,pytzなどの外部ライブラリを用いるのがスタンダード。
python3.9からは、zoneinfoが標準ライブラリに加わり、タイムゾーンを扱いやすくなった。

日付はawareな日付とnativeな日付が存在する。
awareな日付は、timezone（現地時間）情報を持つため、地域が異なる日付同士で比較することができる。
nativeな日付は、timezoneを持たないため、暗黙的に同一の地域とみなせる狭いスコープでのみ利用される。
常に、awareな日付を作成することで、nativeを考慮しない方が明瞭に課題を解決できる。

とにかく、datetimeオブジェクトを作成時は,utcを指定しておくのがよい。
現地時間を重視する場合は、timezoneを指定しておき、表示の際にどの地域を軸に表示するか指定する。

## 日付関連型
```
import datetime
import time

dt = datetime.datetime.now() # datetime型　日付＋時間
da = datetime.date.today()  # date型　日付のみ date型はdatetime型と互換性がある
td = datetime.timedelta(days=1)  # timedelta型　日数、秒数、マイクロ秒数情報を持ち、差分表現に用いられる

ut = time.time()  # float型 UNIX時間（エポック秒）


# 単純に日付を作成しただけではawareな日付にならない
dt = datetime.datetime.now()
dt.tzinfo
# =>> None

dt = datetime.datetime.utcnow()
dt.tzinfo
# =>> None

# 面倒だが常にタイムゾーンを意識する
dt = datetime.datetime.now(tz=datetime.timezone.utc)
dt.tzinfo
# =>> datetime.timezone.utc

# OSの現在日時が2020年11月17日23時の場合の結果は以下のようになる
dt.isoformat()
# =>> '2020-11-17T14:01:54.567154+00:00'

# python3.9からはこう書けるようになる？？
dt = datetime.datetime.now(tz="utc")
```


# 型チェック

``` python

# typeは、完全一致での型チェックを行う場合に利用する。
# isinstanceは、親オブジェクトを含んだ型チェックを行う場合に利用する。
# クラスはタイプのインスタンス

class SampleBase():
  pass

class Sample(SampleBase):
  def __call__(self):
    pass

def func():
  pass

obj = Sample()

type(obj) is Sample # True
type(obj) is SampleBase # False
isinstance(obj, Sample) # True
isinstance(obj, SampleBase) # True
isinstance(Sample, type) # True
issublass(Sample, SampleBase) # True
issublass(Sample, type) # False


# クラスはtypeのインスタンスである
isinstance(SampleBase, type) # True
isinstance(Sample, type) # True

# インスタンスはobjectである
isinstance(obj, object) # True
isinstance(Sample, object) # True
isinstance(obj, type) # False

# callableチェック
objbase = SampleBase()
callable(objbase) # False
callable(obj) # True
callable(func) # True

# asyncチェック
def func_async():
  pass

asyncio.iscoroutinefunction(func) # False
asyncio.iscoroutinefunction(func_async) # True
```


# デコレータ
関数やcallableなオブジェクトはデコレータとして呼び出すことができる。

## デコレータの種類
デコレータが呼び出されるシチュエーションはいくつか考えられ、完全なデコレータを実装するにはそれぞれシチュエーションに応じた実装が必要。

``` python

# 1. 引数なしデコレータ
@deco
def myfunc():
  pass

# 2. 引数なしラッパー関数経由デコレータ
@deco()
def myfunc():
  pass
  
# 3. 引数ありラッパー関数経由デコレータ
@deco(name="test")
def myfunc():
  pass

# 4. 1〜3全対応
全対応に対応すると、引数に意図しない対象がセットされてしまう恐れがあり、また、その意図の判定は困難なため、
引数あり、引数なしを限定させた方がよい。

```

## 実装例
``` python

# デコレータをクラスで実装
class function_decorator():
  def __call__(self, *args, **kwargs):
    is_func_only = (len(args) == 1) and callable(args[0])

    # 引数付きデコレータ処理
    if not is_func_only:
      self.args = args
      self.kwargs = kwargs

      def wrapped(*args, **kwargs):
        return self.__call__(*args, **kwargs)

      return wrapped

    # 括弧省略時 or 引数付きデコレータ処理後（最終処理）
    else:
      if not hasattr(self, "args"):
        self.args = ()

      if not hasattr(self, "kwargs"):
        self.kwargs = {}

      func = args[0]
      self.valid_args(*self.args, **self.kwargs)
      self.on_wrapped(func)
      result = self.return_object(func)
      return result
      
  def valid_args(self, *args, **kwargs):
    pass

  def on_wrapped(self, func):
    print("initializing...")
    print(self.args)
    print(self.kwargs)
    print("initialized.")

  def return_object(self, func):
    return wraps(func)(partial(self.wrapper, func))

  def wrapper(self, func, *args, **kwargs):
    print(args)
    print(kwargs)
    return func(*args, **kwargs)


deco = function_decorator()

@deco(tag="test")
def message(msg):
  print(msg)

message("hello")
```

# Linq
[python-linq-samples](https://github.com/rogerwcpt/python-linq-samples)


# データ構造

## リスト、タプル、ディクショナリ、セット
``` python
arr = [1, 2, 3] # インデックスによるアクセスが可能
tup = (1, 2, 3) # インデックスによるアクセスが可能　インスタンス作成後の値変更は許可されない
set = {1, 1, 2, 3} # インデックスによるアクセスが可能　重複は排除される
dic = {"name": "test", "age": 20} # キーによるアクセスが可能
```


# その他
あまり知られていないpythonの仕様を紹介する。
紹介する仕様は、バージョンによっては利用できない。（主に、Python3.4以降の仕様を紹介）



## 複素数
``` python
c1 = 1 + 1j
c2 = 1 - 2j
c1 + c2 # -> 2 - j
```

## ステップフィルター
``` python
arr = list(range(20))
arr[::2] # -> [0, 2, 4, ...]
```

## Ellipsis
dummy用のオブジェクトを作成する
``` python
... # -> Ellipsis
bool(...)
```


## unpack argument
関数に引数を渡すとき、リストや辞書を引数に当てはめて呼び出したいときがある。
そのようなケースで、配列や辞書を位置限定引数として（\*）、または、キーワード引数として（\*\*）展開することができる。

``` python
def print_profile(name, age):
  print(f"{name} {age}")

arr = ["test", 20]

dic = {
  "name": "test",
  "age": 20
}

print_profile(*arr)
print_profile(**data)

```

## 位置限定引数 / キーワード限定引数
引数は、位置引数とキーワード引数に都合よく解釈されるが、位置限定引数(/の前)、キーワード限定引数(\*の後)として解釈を限定することができる。
位置限定引数は、python3.8から利用可能。

位置限定引数はあまり使い道がないが、キーワード限定引数はコーディングを明示的にするため積極的に活用すべき。
```
def example(pos_only, /, standard, *, kwd_only):
  print(pos_only, standard, kwd_only)
```


## global
どのスコープからでもグローバル変数を定義、参照することができる
``` python
def func():
  global a
  a = 1
```

## nonlocal
ひとつ外側のスコープに属する変数へアクセスができる
``` python
def func():
  count = 0
  
  def func_inner():
    nonlocal count
```

## yield from
他のイテレータから値を返すジェネレータを作成する
``` python
def func():
  yield from range(5)

def func():
  yield from () # 空のイテレータ
```

## raise from
例外を連鎖させる際に、送出元の例外を保持する
``` python
try:
  raise Exception("e1") from Exception("e2")

except Exception as e:
  print(e.__cause__) # e2
```

## async await
```
import asyncio

```
