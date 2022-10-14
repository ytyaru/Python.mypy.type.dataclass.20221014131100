Pythonで静的型チェックする【mypy】

　dataclassを使いたい。型アノテーションを使う。それを静的チェックするにはリントツール`mypy`が必要。という経緯。

<!-- more -->

# ブツ

* [リポジトリ][]

[リポジトリ]:https://github.com/ytyaru/Python.mypy.type.dataclass.20221014131100

## 実行

```sh
git clone https://github.com/ytyaru/Python.mypy.type.dataclass.20221014131100
cd Python.mypy.type.dataclass.20221014131100/src
./run.sh
```

# dataclass

　C言語でいう構造体のこと。

* [dataclasses][]
	* [dataclass][]
	* [field][]

[dataclasses]:https://docs.python.org/ja/3/library/dataclasses.html
[dataclass]:https://docs.python.org/ja/3/library/dataclasses.html#dataclasses.dataclass
[field]:https://docs.python.org/ja/3/library/dataclasses.html#dataclasses.field

　これはORMを書くときに使われると思う。たとえばSQLite3のようなRDBMSのレコードやWebAPIのJSONを表すとき等。複数のデータをPythonの型として扱える。

　特に***型アノテーション***を用いて定義する所が特徴。`id: int`と書くことで変数`id`は`int`型だと示せる。その意義は大きい。コードの大規模化に伴い静的型付けもてはやされる中、Pythonも型アノテーションでその波に乗ろうということだろう。でも残念ながら言語仕様にはない。リンタ（静的チェックツール）は外部ライブラリがないとできないという残念仕様。そこでmypyを使う。

* dataclassでレコードを表現したい
    * 型アノテーションを使わないと意味がない
        * 型アノテーションで書いた静的型チェックは外部ツールを使わないと意味がない

　すごく残念な構図。dataclassはPython標準なのに外部ツールに頼らないとその意味がないという。標準で実装してほしかったが、しょせん後付なので仕方ない。

# mypyをインストールする

```sh
pip install mypy
```

# コードを書く

```python
from dataclasses import dataclass, field
@dataclass
class Record:
    item_ids: list[int]

Record([1,2,3])
Record(['A','B']) # ここでエラーになってほしい
```

# 実行する

　20秒くらいかかる。遅い。

```sh
$ mypy record.py
```
```sh
record.py:7: error: List item 0 has incompatible type "str"; expected "int"
record.py:7: error: List item 1 has incompatible type "str"; expected "int"
Found 2 errors in 1 file (checked 1 source file)
```

　OK！　問題箇所のファイル名、行数、位置もメッセージから読み取れる。エラーの理由も英語だけど書いてる。

　これをPython標準で実装してほしかった。

# まとめ

　Pythonのコードを書くときは以下のような工程になる。

1. コードを書く
1. コードの品質確認する
    1. リントツールをクリアする
    1. 単体テストコードを書く
    1. 単体テストをクリアする

　面倒くさい。一発でリントもテストも実行してクリア判定してほしい。自分でスクリプトを書くことになるのだろう。やることが増えてゆく。

　統合環境みたいなのもあるんだろうけど重いし環境依存あるし。

