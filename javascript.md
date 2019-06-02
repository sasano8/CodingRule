
# thisのスコープ
thisのスコープは以下の種類がある。
- メソッド呼び出しパターン
- 関数呼び出しパターン
- コンストラクタ呼び出しパターン
- apply,call呼び出しパターン

また、注意点として、function宣言時にfunction構文とアロー演算子によって、thisのスコープが異なる。
function構文の場合は、宣言時のオブジェクトのthisを参照するが、アロー演算子による定義はグローバルthisを参照する。

``` JavaScript
this.data = "test"

var f1 = {
    do: function() {
        console.log(this.data)
    }
}

var f2 = {
    do: () => {
        console.log(this.data)
    }
}
```


# コストラクタ
JavaScriptにはクラスの概念がない。
しかし、new演算子でクラスのようなオブジェクトを実現することができる。

``` JavaScript
function Dog(name, cry){
  //this = {}
  this.name = name;
  this.bark = function() {
    console.log(cry);
  };
  //return this;
}

//newは暗黙的に、thisにオブジェクトを格納し、それをリターンします。
var dog = new Dog('poti','bow');

dog.bark();
```
