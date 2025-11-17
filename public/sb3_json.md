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

## project.json

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

プロジェクトのメタデータ

- **agent**
  - エディターのユーザーエージェント
- **sember**
  - 常に `3.0.0`。
- **vm**
  - エディタのバージョンを表す。