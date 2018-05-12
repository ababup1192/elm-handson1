# Elm入門ハンズオン

![](./elm-logo.png)

文法編

---

## このハンズオンのターゲット

- Elmを一切知らない方
    - 始めたばかりで右も左もわからない方
- フロントエンド開発が初めての方
    - サーバサイドをやっている方には特にオススメ
- 関数型に易しく入門したい方
    - WEB(実務)アプリケーションを作ることに特化しており、その為に必要な
      関数型のエッセンスが集約されています

---

## このハンズオンの目的

- Elmを認知してもらいたい
    - 「知ってますか？」「名前だけ…」と回答されることがほとんど 文法面だけでも特徴を覚えてもらいたい
- Elmの考えを引き出しの一つにしてもらいたい
    - 他の言語でも、Elmの実務指向は必ず役に立ちます！
    - 個人でもプロダクトでも選択肢の一つに！
- Elmで気持ちよくなってもらいたい
    - 今までの言語と脳の使い方が違う(かも?)、だけどきもちい！！！


---

## TODO 目次予定

- 式
- 型クラス(モドキ)
- 基礎テクニック
    - 再帰
    - 高階関数

---

## Elmのインストール方法

```
# node.jsは入っている前提です

$ npm install -g elm
$ elm -v
0.18.0
```


---

## Elmのインストールで入るもの

elm-make
Elmのコードを直接コンパイルするコンパイラ
elm-repl
elmの式をその場で評価して確認できるツール
elm-reactor
ブラウザで素早く確認できるビルド・実行環境
elm-package
Elmのパッケージ管理ツール

---

## 資料の進め方

```elm
$ elm-repl
-- コメント REPLを起動後、最初の評価は遅いです。
> "REPLで式を打ち、評価値・型を見ながら文法を確認していきます。"
"REPLで式を打ち、評価値・型を見ながら文法を確認していきます。" : String
```

---

## 式とは

何でしょうか？
評価されることで値になるもの
値も式
利点はなんでしょうか？
文とは違い、式は式に連続して渡すことができる
一時変数を減らす(名付けのコストを減らす)
テスタビリティが上がる
純粋関数の為 式(を評価した値)の等価性が保証される

---

## 式の種類

リテラル
関数
条件式
if
case
よく使われる型
Let式

---

## リテラル

```elm
> True
True : Bool
> False
False : Bool
> 42
42 : number -- IntもしくはFloatである型。
> 3.14
3.14 : Float
```

---

## リテラル(2)

```elm
> 'a'
'a' : Char
> "abc"
"abc" : String
-- 複数行文字列(REPLで複数行入力するには、\を行末に付けます)。
> ""\
| hello\
| world\
| ""
"\n  hello\n  world\n  " : String
```

---

関数(1)

```elm
-- function arg1 arg2 = body
> square n = n^2
<function> : number -> number -- number型の値を受け取り、number型の値を返す関数。
square 5
> 25
```

```elm
-- 実際にソースコードに書く場合には、意図せぬ型推論が働くのを防いだり、
-- ドキュメンテーションのために関数の型は明記しましょう。
square : Int -> Int
square n = n^2
```

---

## 関数(2)

```elm
> stdBMI = 22.0 -- 22.0(Float)を返す関数。変数ではない。その為、再代入不可能。
> bmi h = (h ^ 2) * stdBMI
<function> : Float -> Float
-- 以下のように評価されていきます。
> bmi 1.75 -- 束縛変数(仮引数)hを実引数1.75で束縛します。
(1.75 ^ 2) * stdBMI -- 本体の式を評価していきます。↑と合わせて「関数適用」と呼びます。
3.0625 * 22.0
67.35

-- グローバル変数・一時変数・キーボード入力・ファイル読み込みなどは、関数に一切含まれない。そのような関数を「純粋関数」と呼びます。
```

---

## 関数(3)

```elm

-- 以下のように関数から名前を取り除き、
-- \変数 -> 本体の式
-- のカタチをλ抽象(ラムダ抽象, ラムダ式、ラムダ関数、匿名関数)と呼びます。
> (\h -> (h ^ 2) * stdBMI) 1.75
67.375 : Float
> bmi = (\h -> (h ^ 2) * stdBMI) -- λ抽象自体も式なので名前を付けられます。
-- 後述しますが、式なので別の関数や式に渡すことも、関数の結果としてλ抽象を返すこともできます。このような特徴を持った言語の関数を「第一級関数」(ファーストクラスファンクション)と呼びます。
```

---

## 関数(4)

Elmドキュメント: Basicsパッケージ の組み込み関数を試してみましょう

```elm
> negate 5
-5 : number
> abs -10
10 : number
> sqrt 2
1.4142135623730951 : Float
> round 4.5
5 : Int
> max 1 5 -- 2引数以上受け取る場合にはスペース区切りで渡します。
5 : number
```

---

## 関数(5)

```elm
> 10 + 5 -- 二項演算子は中置記法が使える関数です。
15 : number
> (+) 10 5 -- 丸括弧で括ることで、通常の関数のように前置記法で記述できます。
15 : number
> True && False
False : Bool
> 1 < 3
True : Bool
> 1 == 2
False : Bool
> 1 /= 2
True : Bool
```

---

## 関数(6)

```
-- (/) : Float -> Float -> Float
> 5 / 2
2.5 : Float
— (//) : Int -> Int -> Int
> 5 // 2
2 : Int
-- 2はFloatとみなされる
> 5.5 / 2
2.75 : Float
> 5 // 2.5 -- TYPE MISMATCH
```

---

## プチ演習: 関数

- Elmドキュメント: Basicsパッケージ の関数をいくつか試してみましょう
- λ抽象で関数を定義してみましょう

---

## 条件式 if(1)

```elm
-- ifは文ではなく式のため評価されることにより値になり、else節の省略は出来ません。
> powerLevel = 10000
10000 : number
> if powerLevel > 9000 then "OVER 9000!!!" else "meh"
"OVER 9000!!!" : String
-- 値なので、別の関数の引数として渡すことも可能
> String.length (if powerLevel > 9000 then "OVER 9000!!!" else "meh") 
12 : Int
```

---

## 条件式 if(2)

```elm
> key = 40
> n = 10
-- else節にif式をネストすることも可能。大きくスペースを取るインデントも見やすい。
> if key == 40 then\
    n + 1\
\
else if key == 38 then\
    n - 1\
\
else\
    n
11 : number_
```

---

## プチ演習: 条件式

```elm
if同士の演算を試してみましょう。
```

---

## 特殊型 List(1)

```elm
[] -- Empty
[1] -- 1 :: [] => Node 1 (Empty)
[1, 2, 3] -- 1 :: 2 :: 3 :: []

> 1 :: 2 :: 3 :: [] -- (::) は演算子で右から評価され、Listの先頭に値を追加します。
[1, 2, 3] : List number
> (::) 1 ((::) 2 (((::) 3 []))) -- Node 1 (Node 2 (Node 3 (Empty)))
[1,2,3] : List number
```

---

## 特殊型 List(2)

```elm
-- Debug.crashは、例外を発生させる関数です。本来はTODO用途で使いましょう。
-- 実際のList.headなど通常の組み込み関数では実行時例外は起きません。確認してみましょう。
> myHead list =\
    case list of\
        [] ->\
            Debug.crash "No such Element"\
\
        x :: xs ->\
            x
-- a は、
<function> : List a -> a
```

---

## 特殊型 List(3)

```elm
> myHead [1, 2, 3]
1 : number
> myHead [1]
1 : number
> myHead (1 :: [])
1 : number
> myHead []
Error: Ran into a `Debug.crash` in module `Repl`
...
The message provided by the code author is:

    No such Element
```

## プチ演習: List

- Listパッケージの関数を試してみましょう。
- List.isEmpty, List.tail 関数をパターンマッチを利用して myIsEmpty, myTailを定義してみましょう。

---

## 特殊型 Union Types(1)

```
-- UnionTypesは、stringやbool, nullをよりも直感的に分岐した型を表すことができます。
-- Tagged UnionやADT(Abstract Data Type: 抽象データ型)と呼ばれます。
-- Dollar, EURO, JPY はMoney型を返す関数で値コンストラクタ呼ばれます。
> type Money = Dollar | EURO | JPY
> Dollar
Dollar : Money
> EURO
EURO : Money
> JPY
JPY : Money
> JPY == JPY -- Union Typesは等価性を備えています。
True : Bool
```

---

## 特殊型 Union Types(2)

```
toJPYRate : Money -> Float
toJPYRate money =\
-- すべての分岐が無ければコンパイルエラー。つまり網羅性を保証しています。
    case money of\
        Dollar ->\
            109.134\
\
        EURO ->\
            129.462\
\
        JPY ->\
            1
```

---

## 特殊型 Union Types(3)

```elm
> type Foo = Bar Int
-- 分岐の無いUnion Typeの場合は関数の引数でパターンマッチできます。括弧を忘れずに!
> getBarNum (Bar num) = num
<function> : Repl.Foo -> Int
> getBarNum (Bar 5)
5 : Int
```

---

## 特殊型 Union Types(4)

```elm
-- 頻繁に使う値があるか無いかの分岐に使うMaybe型です。
getNumOrZero : Maybe Int -> Int
> getNumOrZero mNum =\
    case mNum of\
        Just n ->\
            n\
\
        Nothing ->\
            0

> getNumOrZero (Just 1)
1 : number
```
