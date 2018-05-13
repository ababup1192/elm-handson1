## Elm入門ハンズオン

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

## 目次

- 式
- パターンマッチ
- Union Types
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

- elm-make
    - Elmのコードを直接コンパイルするコンパイラ
- elm-repl
    - elmの式をその場で評価して確認できるツール
- elm-reactor
    - ブラウザで素早く確認できるビルド・実行環境
- elm-package
    - Elmのパッケージ管理ツール

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

- 何でしょうか？
    - 評価されることで値になるもの|
    - 値も式|
- 利点はなんでしょうか？|
    - 文とは違い、式は式に連続して渡すことができる|
        - 一時変数を減らす(名付けのコストを減らす)|
    - テスタビリティが上がる|
        - 純粋関数の為 式(を評価した値)の等価性が保証される|
---

## 式の種類

- リテラル
- 関数
- let式
- if式
- パターンマッチ(case式)
- Union Types
---

## リテラル

```elm
> True
True : Bool
> False
False : Bool
> 42
42 : number -- IntもしくはFloatである型
> 3.14
3.14 : Float
```

+++

## リテラル

```elm
> 'a'
'a' : Char
> "abc"
"abc" : String
> """\
| hello\
| world\
| """
"\n  hello\n  world\n  " : String
```
@[5-8](REPLで複数行入力するには、\を行末に付けます。)

+++

## リテラル

```elm
> [1, 2, 3, 4, 5]
[1,2,3,4,5] : List number
> [1, "a"]
      ^^^
The 1st entry has this type:
    number
But the 2nd is:
    String
> []
[] : List a
```
@[1-2](Listを表すリテラルです。リストはある型の要素が0個以上の集まってできる型です。)
@[3-8](リストに入る型は任意ですが、リスト全体で単一の型で無ければなりません。)
@[9-10](REPL上では空のリストを作った場合に、どのような型かを予測することはできません。)
@[9-10](Elmでは任意の型を表すとき「a, b, c, ...」のように、予約語を除いた小文字から始まる**型変数**を利用します。)
@[9-10](他の言語で言う、ジェネリクスにおける型パラメータなどと同じ意味を持ちます。)

+++

## リテラル

```elm
> (1, 2, 3, 4, 5)
(1,2,3,4,5) : ( number, number1, number2, number3, number4 )
> (1, "1", True, [(1, 2), (3, 4)], [["a", "b"], ["c", "d"]])
(1,"1",True,[(1,2),(3,4)],[["a","b"],["c","d"]])
    : ( number
      , String
      , Bool
      , List ( number1, number2 )
      , List (List String)
      )
> ()
() : ()
```
@[1,2](タプルを表すリテラルです。タプルは複数の型の0個以上の要素からなる型です。)
@[1,2](numberのように不定の型の場合、要素ごとに別物として表すため違うnumber型として推論されます。)
@[3-10](複雑なタプルの例)
@[11-12](空のタプルは**Unit**とも言われ、何もない型を表すときに使われます。)

+++

## リテラル

```elm
> record = {x = 1, y = 2, z = 3}
{ x = 1, y = 2, z = 3 } : { x : number, y : number1, z : number2 }
> record.x
1 : number
> .y record
2 : number
> { record | x = 10, z = record.y + 20 }
{ x = 10, y = 2, z = 22 } : { y : number1, x : number, z : number1 }
```
@[1-2](レコードはkeyとvalueからなるセットです。JavaScriptのオブジェクトに似ています。)
@[3-4](JavaScriptのオブジェクトのように、*record*.x のようにアクセスできます。)
@[5-6](また、*.field*のような関数がレコードに対して生成されます。)
@[7-8]({ レコード変数 | 変数束縛 } の形でレコードの値を変更した、新しいレコードを生成します。)

---

## 関数

```elm
> square n = n^2
<function> : number -> number
> square 5
> 25
```
@[1](function arg1 arg2,... = body)
@[2](number型の値を受け取り、number型の値を返す関数。)
@[3](関数squareの束縛変数(仮引数)nを実引数5で束縛します。)
@[3-4](本体が評価されることで、25となります)
@[1-4](関数定義と評価)

+++

## 関数

```elm
square : Int -> Int
square n = n^2
```
実際にソースコードに書く場合には、意図せぬ型推論が働くのを防いだり、ドキュメンテーションのために関数の型(シグネチャ)は明記しましょう。

+++

## 関数

```elm
> type alias Name = String
> type alias Age = Int
> type alias User = { name: Name, age: Age }
-- getName : User -> Name
> getName user = user.name
<function> : { b | name : a } -> a
getName (User "john" 15)
"john" : Repl.Name
```
@[1-3](より可読性の高いコードを書くためには、*type alias*で型の別名を付けることもできます。Elmでは型の設計をおこなってから実装を考える型駆動開発の考え方も有効です。)
@[3,7](レコード型のtype aliasは少し特殊な事情があります。(レコードalias フィールドの値, フィールド値, ...) のように本来の書き方をショートカットできます。)


+++

## 関数

```elm
> stdBMI = 22.0
> bmi h = (h ^ 2) * stdBMI
<function> : Float -> Float
> bmi 1.75
(1.75 ^ 2) * stdBMI
3.0625 * 22.0
67.35
```
@[1](22.0(Float)を返す関数。変数ではない。その為、再代入不可能。)
@[4-7](関数bmiは以下のように評価されます。)
@[2,4](関数bmiの束縛変数(仮引数)hを実引数1.75で束縛します。)
@[4-5](本体の式を評価していきます。束縛し評価することを「関数適用」と呼びます。)
@[6-7](あとは、stdBMIを評価した値を束縛し、計算式を評価していきます。)
@[1-7](グローバル変数・一時変数・キーボード入力・ファイル読み込みなどは、関数に一切含まれない。そのような関数を「純粋関数」と呼びます。)


+++

## 関数

```elm
> (\h -> (h ^ 2) * stdBMI) 1.75
67.375 : Float
> bmi = (\h -> (h ^ 2) * stdBMI)
```

@[1-2](このように関数から名前を取り除き、)
@[1-2](\変数 -> 本体の式)
@[1-2](このカタチを匿名関数(ラムダ式、ラムダ関数、ラムダ抽象)と呼びます。)
@[3](λ抽象自体も式なので名前を付けられます。)
@[1-3]( 後述しますが、式なので別の関数や式に渡すことも、関数の結果としてλ抽象を返すこともできます。
@[1-3](このような特徴を持った言語の関数を「第一級関数」(ファーストクラスファンクション)と呼びます。)

+++

## 関数

Elmドキュメント: [Basics](http://package.elm-lang.org/packages/elm-lang/core/5.1.1/Basics)パッケージ の組み込み関数を試してみましょう

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

+++

## 関数

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

+++

## 関数

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

+++

## let式

```
> let a = 10 in a + 5
15 : number
> let px x = toString x ++ "px" in (px 10, px 5)
("10px","5px") : ( String, String )
```
@[1-2](let式は、let *変数束縛* in *式*と言うように書きます。スコープを限定し式なのでそのまま評価されます。)
@[2-3](let式の特徴として、変数束縛時にローカル関数を定義することができます。)


+++

## let式

```
> circle r =\
|     let\
|         pi =\
|             3.14\
| \
|         circumference =\
|             2 * pi * r\
| \
|         area =\
|             pi * r ^ 2\
|     in\
|         "Circle(circumference = " ++ (toString circumference) ++ ", area = " ++ (toString area) ++ ")"
<function> : Float -> String
> circle 3
"Circle(circumference = 18.84, area = 28.26)" : String
```
実際には、let式は関数中でこのように使われます。

+++

## プチ演習: 関数

- Elmドキュメント: [Basicsパッケージ](http://package.elm-lang.org/packages/elm-lang/core/latest/Basics) の関数をいくつか試してみましょう
- λ抽象で関数を定義してみましょう
- let式を使った関数を定義してみましょう。
---

## if式

```elm
> powerLevel = 10000
10000 : number
> if powerLevel > 9000 then "OVER 9000!!!" else "meh"
"OVER 9000!!!" : String
> String.length (if powerLevel > 9000 then "OVER 9000!!!" else "meh")
12 : Int
```
@[3-4](ifは文ではなく式のため評価されることにより値になり、else節の省略は出来ません。)
@[5-6](値なので、別の関数の引数として渡すことも可能)

+++

## if式

```elm
> key = 40
> n = 10
> if key == 40 then\
    n + 1\
\
else if key == 38 then\
    n - 1\
\
else\
    n
11 : number
```

@[3-10](else節にif式をネストすることも可能。大きくスペースを取るインデントも見やすい。)

+++

## プチ演習: if式

- if同士の演算(if式同士の足し算など)を試してみましょう。

---

## パターンマッチ

```elm
fib n =\
    case n of\
        0 ->\
            1\
\
        1 ->\
            1\
\
        _ ->\
            fib (n - 1) + fib (n - 2)
> fib 10
89 : number
```
*case* 対象 *of*、 パターン -> のように書くことで、様々なパターンをマッチさせることができます。case自体ももちろん式なので値を返します。本質は分岐をおこなうswitch-case分とは大きく異なります(次ページ)。

+++

## パターンマッチ

```elm
> tuple = (8, 4)
(8, 4) : ( number, number1 )
> case tuple of\
|     ( x, y ) ->\
|         x + y
12 : numbner

record = { x = 7, y = 8 }
> case record of\
|     { x, y } ->\
|         x + y
15 : number
```
パターンマッチの本質はデータ構造の分解です。データ構造が構造通り直感的に取り出せていることがわかります。

+++

## パターンマッチ

```elm
> plusPair (x, y) = x + y
<function> : ( number, number ) -> number
> plusPair (4, 4)
8 : number
> (\(x, y) -> x + y) (4, 4)
8 : number
> let {x, y} = { x = 4, y = 4 } in x + y
8 : number
> let ({x, y, z} as record) = { x = 1, y = 2, z = 3 } in { record | y = x * y, z = x + z }
{ x = 1, y = 2, z = 4 } : { x : number2, y : number, z : number1 }
```
@[1-8](分岐の存在しないデータ構造に関しては、関数・無名関数・let式等でパターンマッチして取り出すことができます。)
@[9-10](パターンマッチの際に分解前のデータ構造そのものを扱いたいときには、**as**キーワードを使い変数に束縛します。もちろんlet式以外でも使えます。)

+++

## パターンマッチ

```elm
-- getNumOrZero : Maybe Int -> Int
> getNumOrZero mNum =\
    case mNum of\
        Just n ->\
            n\
\
        Nothing ->\
            0
> let f x = if x > 5 then Just x else Nothing in getNumOrZero(f 10)
10 : number
> let f x = if x > 5 then Just x else Nothing in getNumOrZero(f 3)
0 : number
> 
```

頻繁に使う値があるか無いかの分岐に使うMaybe型です。

+++

## パターンマッチ

```elm
> safeSqrt n =\
    if n >= 0 then\
        Ok n\
    else\
        Err "n must be a positive number."
> safeSqrt 1
Ok 1 : Result.Result String number
> safeSqrt -1
Err "n must be a positive number." : Result.Result String number
```

Maybeと似た型ですが、失敗したときの理由を任意の型で受け取ることができます。

---

## Union Types

```
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
@[1](UnionTypesは、stringやbool, nullをよりも直感的に分岐した型を表すことができます。)
@[1](Tagged UnionやADT(Abstract Data Type: 抽象データ型)と呼ばれます。)
@[1](Dollar, EURO, JPY はMoney型を返す関数で値コンストラクタ呼ばれます。)

+++

## Union Types

```
toJPYRate : Money -> Float
toJPYRate money =\
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
@[3-11](すべての分岐が無ければコンパイルエラー。つまり網羅性を保証しています。)

+++

## Union Types

```elm
> type Foo = Bar Int
> getBarNum (Bar num) = num
<function> : Repl.Foo -> Int
> getBarNum (Bar 5)
5 : Int
```

分岐の無いUnion Typeの場合は関数の引数でパターンマッチできます。括弧を忘れずに!

---

## 再帰



---

## List

```elm
data List a = Empty | Node a (List a)
```
Listは以上のようなUnion Typesと見なすことができます。

+++

## List

```elm
[] -- Empty
[1] -- 1 :: [] => Node 1 (Empty)
[1, 2, 3] -- 1 :: 2 :: 3 :: []

> 1 :: 2 :: 3 :: []
[1, 2, 3] : List number
> (::) 1 ((::) 2 (((::) 3 []))) -- Node 1 (Node 2 (Node 3 (Empty)))
[1,2,3] : List number
```

(::) は演算子で右から評価され、Listの先頭に値を追加します。

+++

## List

```elm
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
@[4](Debug.crashは、例外を発生させる関数です。本来はTODO用途で使いましょう。)
@[1-9](実際のList.headなど通常の組み込み関数では実行時例外は起きません。確認してみましょう。)

+++

## List

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

+++

## プチ演習: List

- [Listパッケージ](http://package.elm-lang.org/packages/elm-lang/core/5.1.1/List)の関数を試してみましょう。
- List.isEmpty, List.tail をパターンマッチを利用して自分で定義してみましょう。
    - myIsEmpty, myTailという名前で定義してみましょう。

