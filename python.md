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
