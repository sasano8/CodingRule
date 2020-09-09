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

## キーワード限定引数
関数の引数定義において、*を宣言すると、それ以降の引数は必ずキーワード引数として呼び出さなければならない。
``` python
def func(a, *, b):
  pass
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


## unpack argument
関数に引数を渡すとき、リストや辞書を引数に当てはめて呼び出したいときがある。
そのようなケースで、配列や辞書を位置限定引数として（*）、または、キーワード引数として（\**）展開することができる。

''' python
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

## position only argument / keyword only argument
引数は、位置引数とキーワード引数に都合よく解釈されるが、位置限定引数(/の前)、キーワード限定引数(*の後)として解釈を限定することができる。
位置限定引数は、python3.8から利用可能。

位置限定引数はあまり使い道がないが、キーワード限定引数はコーディングを明示的にするため積極的に活用すべき。
```
def example(pos_only, /, standard, *, kwd_only):
  print(pos_only, standard, kwd_only)
```
