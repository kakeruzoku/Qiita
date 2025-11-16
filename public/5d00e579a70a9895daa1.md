---
title: ABC430のE問題がPythonで1行で解けた話
tags:
  - Python
  - AtCoder
  - AtCoderBeginnerContest
  - ABC430
  - ABC430E
private: false
updated_at: '2025-11-02T20:29:47+09:00'
id: 5d00e579a70a9895daa1
organization_url_name: null
slide: false
ignorePublish: false
---
昨日ABCでなぜかE問題がさくっと解けたのでいろいろ試してたらなんと1行で書けてしまったのでメモとして。

# 該当の問題

[ABC430 E - Shift String](https://atcoder.jp/contests/abc430/tasks/abc430_e )

[コンテスト中に提出したコード](https://atcoder.jp/contests/abc430/submissions/70600991 )

```py
def main():
    T = int(input())

    for _ in range(T):
        A = input()
        B = input()

        a = A+A
        try:
            print(a.index(B))
        except ValueError:
            print(-1)
main()
```

[1行で書いたコード](https://atcoder.jp/contests/abc430/submissions/70645100 )

```py
print("\n".join([str((input()*2).find(input())) for _ in range(int(input()))]))
```

# 解説

問題はこんな感じ。

> 0, 1 からなる長さの等しい文字列 A,B が与えられます。
> 
> A に対して以下の操作を 0 回以上何度でも行うことができます。
> 
> - A の先頭の文字を末尾に移動させる
>
> A=B とするために必要な最小の操作回数を求めてください。
> 但し、どのように操作しても A=B とできない場合、代わりに −1 と出力してください。
>
> T 個のテストケースが与えられるので、それぞれについて答えを求めてください。
>
> [ABC430E 問題文](https://atcoder.jp/contests/abc430/tasks/abc430_e )

例えば A=`test`,B=`ttes` だとこの操作が3回必要となる。 (`test`->`estt`->`stte`->`ttes`)

この操作って`testtest` (Aを2回繰り返した文字列)のなかから順にみてけばいける感じである。(語彙力不足)

```py
"test    "
" estt   " #見る部分をずらしていく感じ
"  stte  "
"   ttes " #ここで一致する!
"    test"
```

なので`testtest`から`ttes`を探す問題へと変化する。

Pythonには[`str.find()`](https://docs.python.org/ja/3.13/library/stdtypes.html#str.find ) という素晴らしい関数が存在するのでそれを使ってあげれば問題ない。

しかも見つからなかった時に`-1`を返すという部分まで実装されているのは素晴らしい。

ここまでくればコンテスト中にだしたコードは理解できるでしょう。


```py
def main():
    T = int(input()) #テストケース数を受け取る

    for _ in range(T): #テストケースの数だけ繰り返す
        A = input() #Aの入力
        B = input() #Bの入力

        a = A+A #Aを2回繰り返す
        try:
            print(a.index(B)) # Bの場所を探して出力
        except ValueError: #str.index()は見つからなかったときに例外を送出するのでそれをキャッチ
            print(-1) #-1を出力
main()
```

ここからは改善していくだけ。

ここで

```py
try:
    print(a.index(B))
except ValueError:
    print(-1)
```
の部分は `print(a.find(B))` に置き換えることができる。

```py
def main():
    T = int(input())

    for _ in range(T):
        A = input()
        B = input()

        a = A+A

        print(a.find(B)) #findに置き換えた
main()
```

わざわざ`main()`に関数化する必要はないので削除。

```py
T = int(input())

for _ in range(T):
    A = input()
    B = input()

    a = A+A

    print(a.find(B))
```

`A+A`は`A*2`と書けるので

```py
T = int(input())

for _ in range(T):
    A = input()
    B = input()

    a = A*2 #文字列*数字 は文字列を数字回繰り返したものになる ("abc"*3 = "abcabcabc")

    print(a.find(B))
```


`T`,`B` `A`は1回しかつかわないので使うときに取得

```py
for _ in range(int(input())):
    a = input()*2
    print(a.find(input()))
```

`a.find(input())`の`find()`内の`input()`よりも`a`が先に読み込まれるため、`a`も詰められる

```py
for _ in range(int(input())):
    print((input()*2).find(input()))
```

最後に一括で出力するように変更してみる。
[`str.join()`](https://docs.python.org/ja/3.13/library/stdtypes.html#str.join )に入れる引数は`list[int]`だとエラーがでるので`list[str]`を入れる。

```py
r = []
for _ in range(int(input())):
    r.append(str((input()*2).find(input())))
print("\n".join(a)) #改行で区切られている
```

ここで`r`について考えてみると、リスト内表記が使えることに気付く。

```py
#hogehogeに何らかの処理、Nに数字を入れる

r = []
for i in range(N)
    r.append(hogehoge) 

# 上と下は全く同じ結果になる(はず)

r = [hogehoge for i in range(N)]
```

だから

```py
r = [str((input()*2).find(input())) for _ in range(int(input()))]
print("\n".join(r))
```

`r`もまとめられるので

```py
print("\n".join([str((input()*2).find(input())) for _ in range(int(input()))]))
```

おお。なんと1行で書けてしまったのである。

コンテスト中にE問題を1行で提出したらロマンありそうだなぁと思った。特にPythonは改行とインデントに意味があるので難しいだろう。

これ思いつけそうだったのが悔しい。`index()`じゃなくて`find()`を先に見つけていれば書けそうだったなぁ

## 終わりに

運営もしかして狙ったか?
