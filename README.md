# What is this repository

Hugoを使ってみる -> 試した機能をReadmeにまとめる

というリポジトリ。

# Hugo

Go製のSSG。とにかく速い。

# 最初にやること

## スケルトン生成

```
hugo new site hugo-try
```

## テーマの設定

themes以下にテーマをクローンする

```
git clone https://github.com/vimux/mainroad
```

## サイトの設定

```config.toml
baseURL = "http://example.org"
languageCode = "ja-jp"
title = "Hugo Try"
theme = "mainroad"
```

## 記事生成

ルートで

```
hugo new post/first_post.md
```

## サーバー起動

```
hugo server
```

Draftを含めるときは

```
hugo server -D
```

## Front matter

content以下のmdファイルのヘッダー。

```post/first_post.md
---
title: "First_post"
date: 2021-01-08T21:45:11+09:00
draft: true
---

```

## Archetypes

`hugo new` した際には `archetypes/default.md` をベースにmdファイルを生成する。

## Draft

Draftの指定は `draft: true` でできる。
外すときは `draft: false` とするか、 `draft: true` の行自体を消す。
Draftのリストを見るには `hugo list drafts` を実行。

## 本番用構成(public)

```
hugo
```

コマンドで `public` 以下に吐き出す。

```
hugo -D
```

とすると `draft: true` を無視して生成する。

# ShortCode

content以下のmdファイル内から呼び出せ、HTMLを埋め込むことができる。

## 呼び出し方

```
{{< shortcode parameter >}}
```

## Named Parameter

`=` でparameterを渡す。

```
{{< shortcode param_f="val1" param_s="val2" >}}
```

## Paired ShortCode

```
{{< shortcode >}}
.
.
.
{{< /shortcode >}}
```

## 自作ショートコード

`/layouts/shortcodes` 以下に `****.html` という風に作成する。

```original_shortcode.html
<h1>Rendered from `original_shortcode` !</h1>
```

呼び出すときは

```first_post.md
{{< original_shortcode >}}
```

## Parameter

パラメーターは以下のようにして渡す。

```
{{< helloshortcode green hello-world >}}
```

取り出しは以下のようにする。

```/layouts/shortcodes/helloshortcode
<h1 style="color: {{ .Get 0 }};">
  {{ .Get 1 }}
</h1>
```

Named Parameterでは

```/layouts/shortcodes/namedshortcode
<p style="background-color: {{ .Get `bg` }};">
  {{ .Get `text` }}
</p>
```

のようにして取り出す。

### Inner Parameter

```/layouts/shortcodes/inner-param.html
<h1 style="color: blue;">
  {{ .Inner }}
</h1>
```

```inner.md
{{< inner-param >}}
This is a inner text.
{{< /inner-param >}}
```

### Front matterのParamを使う

```title-and-date.html
<h2>Date: {{ $.Page.Params.date }}</h2>
<h2>Title: {{ $.Page.Params.title }}</h2>
```

### ConfigのParamを使う

```config.toml
baseURL = "http://example.org/"
languageCode = "ja-jp"
title = "Hugo Try"
subtitle = "hugo-try"
theme = "mainroad"

[params]
  author = "panorama"
  hololive_en = [
    "Gawr Gura",
    "Takanashi Kiara",
    "Mori Calliope",
    "Ninomae Ina'nis",
    "Watson Amelia"
  ]
```

上記に対して

`{{ $.Site.Title }}` や `{{ $.Site.Params.author }}` のようにして参照できる。

```/content/post/config-params.md
<h1>{{ $.Site.Title }}</h1>

<ul>
  {{ range $.Site.Params.hololive_en }}
    <li>
      {{ . }}
    </li>
  {{ end }}
</ul>

```

### Private ShortCode

```/layouts/shortcodes/private-memo.html
{{ if .Site.IsServer }}
  <p>{{ .Inner }}</p>
{{ end }}
```

```/content/post/memo.md
.
.
.

{{< private-memo >}}
  This is a memo.
{{< /private-memo >}}
```

### Syntax Highlight

PygmentsによるSyntax Highlight

```config.toml
pygmentsCodeFences = true
```

あるいは

`highlight`ショートコードだとより細かい設定もできる。
