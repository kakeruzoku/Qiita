---
title: 【完全攻略】.sb3ファイルを解析してみた
tags:
  - ''
private: false
updated_at: ''
id: null
organization_url_name: null
slide: false
ignorePublish: false
---

この記事ではScratchの`.sb3` ファイルの中にあるjsonファイルについてまとめていく。

:::note warn
この記事はあくまで `.sb3` ファイルについての記事である。実行時の詳細な挙動については他の記事を当たってほしい。
:::

:::note warn
`[?]` の部分は推測で書いている部分なのであまり参考にしないほうがいいかもしれない。
(だからといって他がすべて正しいという保証があるわけではないよ！)

もしその部分についてよく知っているのだったら是非編集リクエストを送ってほしい。
:::


## ファイル自体の構造

`.sb3`の構造はzipファイルである。拡張子をzipに書き換えて解凍すると以下のようなファイル構造となる。

```
/
    0fb9be3e8397c983338cb71dc84d0b25.svg
    83a9787d4cb6f3b7632b4ddfebf74367.wav
    83c36d806dc92327b9e7049a565c6bff.wav
    bcf454acf82e4504149f7ffe07081dbc.svg
    cd21514d0531fdffb22204e0ec5ed84a.svg
    project.json
```

`[0-9a-f]{32}\..+`で表現されるファイルはコスチューム(背景)か音である。`[0-9a-f]{32}`の部分はファイルのMD5 hashである。

アカウントを持っていて作品として保存していれば `https://assets.scratch.mit.edu/{md5hash}.{ext}` からダウンロードできる。

ここからは `project.json` についてまとめていく。

## project.json全体

### 最初に抑えておくべきこと

まずこの記事を読んでいく前に、全体で使われているシステムについて抑えておこう。

#### IDについて

project.jsonでは様々なオブジェクトの識別子として20文字のランダムな文字列を使用している。

具体的には、文字列 ```!#%()*+,-./:;=?@[]^_`{|}~ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789``` の中から文字列を1つ無作為に選ぶことを20回繰り返し、結合した文字列のことである。

例:

```
Yc:^8;:\gxg(1n+~F2O7
{r**.@qWvV2D,xax1I33
:3%_htMV2wdxwJkF_ln_
I2nvOlA];Z&d%{@O37v)
\h3J|Cae,}VIeBfOfk:F
```

Pythonでの実装例:
```py
import random,string
ID_CHARS = string.ascii_letters + string.digits + string.punctuation

def generate_id() -> str:
    return "".join(random.choices(ID_CHARS, k=20))
```

該当のソースコード:

https://github.com/scratchfoundation/scratch-editor/blob/develop/packages/scratch-vm/src/util/uid.js

今後これによって生成されたIDを `UID` と呼ぶ。

### ルート要素

一番外側はこんな感じ。

```json
{
  "extensions":["..."],
  "meta":{"...":"..."},
  "monitors":["..."],
  "targets":["..."]
}
```

### extensions

拡張機能のリスト。形式は`list[str]`

`translate`,`music`,`pen`,`videoSensing`,`text2speech`,`makeymakey`,`microbit`,`ev3`,`boost`,`wedo2`,`gdxfor`,`faceSensing`が使える。

### meta

プロジェクトのメタデータ。

この部分は最後に保存した時に使用していたエディターの情報が使われる。

```json
"meta": {
    "agent": "...",
    "semver": "...",
    "vm": "..."
}
```

- **agent**
  - エディターのユーザーエージェント。オフラインエディターだとChrome? `[?]`
- **semver**
  - 常に `3.0.0`。
- **vm**
  - エディタのバージョンを表す。[package.json](https://github.com/scratchfoundation/scratch-editor/blob/develop/package.json )の`version`の部分。