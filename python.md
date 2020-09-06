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


# クラスとはtypeのインスタンスである
isinstance(SampleBase, type) # True
isinstance(Sample, type) # True

# インスタンスは、objectである
isinstance(obj, object) # True
isinstance(obj, type) # False

# callableチェック
objbase = SampleBase()
callable(objbase) # False
callable(obj) # True
callable(func) # True
```


# デコレータ
関数やcallableなオブジェクトはデコレータとして呼び出すことができる。
デコレータの実装方法は難しいので、以下を参考として欲しい。

``` python

# 1. 引数なしデコレータ
@deco
def myfunc():

# 2. 引数なしラッパー関数経由デコレータ
@deco()
def myfunc():

# 3. 引数ありラッパー関数経由デコレータ
@deco(name="")
def myfunc():

```

## 実装
``` python
# 1. 引数なしデコレータ
def args_logger(func):
    def wrapper(*args, **kwargs):
        func(*args, **kwargs)
    return wrapper



```

# その他
あまり知られていないpythonの仕様を紹介する。
紹介する仕様は、バージョンによっては利用できない。（主に、Python3.4以降の仕様を紹介）

```
# 複素数
c1 = 1 + 1j
c2 = 1 - 2j
c1 + c2 # -> 2 - j

# ステップフィルター
arr = list(range(20))
arr[::2] # -> [0, 2, 4, ...]

# Ellipsis(省略)
... # -> Ellipsis

bool(...) # dummyオブジェクトのように扱える

# キーワード限定引数(関数の引数定義において、*を宣言すると、、それ以降の引数は必ずキーワード引数として呼び出さなければならない。)
def func(a, *, b):
  pass


# global(どのスコープからでもグローバル変数を定義、参照することができる)
def func():
  global a
  a = 1

# nonlocal(ひとつ外側のスコープに属する変数へアクセスができる)
def func():
  count = 0
  
  def func_inner():
    nonlocal count

# yield from(他のイテレータから値を返すジェネレータを作成する)
def func():
  yield from range(5)

def func():
  yield from () # 空のイテレータ


# raise from(例外を連鎖させる際に、送出元の例外を保持する)
try:
  raise Exception("e1") from Exception("e2")

except Exception as e:
  print(e.__cause__)



```
