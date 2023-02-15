# 創造大学 コーディング仕様

## ページ設計
### レスポンシブのブレークポイント基準

* SP: 〜639px
* TAB: 640px〜1023px
* PC: 1024px〜
※TAB−PC間の表示切り替えは無しでも可

### twigファイルのディレクトリの切り方
下記ルールで統一
```
(URL階層名)/[index]もしくは[URL階層の最終名].html.twig
```

例えば
```
/level01
/level01/subpage1
/level01/subCategory1
/level01/subCategory1/subpage1
/level01/subCategory1/subpage2
/level01/subCategory2
/level01/subCategory2/subpage1
```

の様な階層のページがあるとして

```
pages/
  level01/
    index.html.twig  ( /level01 )
    subpage1.html.twig ( /level01/subpage1 )

    subCategory1/
      index.html.twig ( /level01/subCategory1)
      subpage1.html.twig ( /level01/subCategory1/subpage1 )
      subpage2.html.twig ( /level01/subCategory2/subpage2 )

    subCategory2/
      index.html.twig ( /level02/subCategory1)
      subpage1.html.twig ( /level02/subCategory1/subpage1 )
```
とする。
またコンテンツでファイル分割しているtwigファイルがある場合は、
```
_(親ファイル名)_(子ファイル名).html.twig
```
のファイル名で作成。例えば上記例で
level01/index.html.twig内でincludeして読み込む子ファイルがある場合、ファイル構成は

```
pages/
  level01/
    index.html.twig
    subpage1.html.twig
    _index_child01.html.twig (indexに読み込まれる子ファイル)
    …（略）…

```
たとえばsubpage1にも子ページがある場合は、indexの子ページファイルと同階層に設置されますが、ファイル名のプレフィックス(_親ファイル名)部分で関連を判断します。

### ページのネームスペース指定
* bodyタグにIDで指定する
* ID名はURL階層をアンダーバーで区切り
(ID名をパラメータ渡ししてください)
    /company
    ```
    <body id="company_index">
    ```
    /company/faq
    ```
    <body id="company_faq">
    ```

    ※ 実際のコーディング時には各ページの_page変数で指定します。
    
    ```
    ** pagefile.html.twig

    {% extends "pages/base.html.twig" %}
    {% set _page = "xxxx" %} ←ここ
    …
    ```
    _pageで指定したID名が親ファイルのpages/base.html.twig内のbodyタグで
    ```
    <body id="xxxx">
    ```
    として展開されます。

### ページ内セクションのクラス名
* pg-(クラス名)で指定
* クラス名はページ内で固有の名前にする。サイト全体で固有の名前でなくてもOK

    ```
    <div class="pg-section01">
    　…
    </div>

    <div class="pg-section02">
    　…
    </div>
    ```

### コンポーネントのクラス名
* c-(ディレクトリ名)-(コンポーネント名)で指定する
    components/company/faq.html.twig

    ```
    <div class="c-company-faq">
    ```

## CSS設計
### フォントスタイルセットの利用
* 基本はscss/object/utility/_text_style.scssに用意したフォントスタイルセットを利用
* 用途とスタイルが一致するものがないときは全パラメータを個別に設定
* 既存スタイル継承の部分上書きはしない！(後々の既存スタイルセット変更に対応できない)
* スタイルセットが一致していても用途が異なる場合は使わない（同上）

### margin,padding,幅高さ等のpx指定
* 基本テキストサイズの可変に対応させる場合：
    → scss/global/functions/_size.scssに記載のsize関数で指定

    ```
    ** pages/sample.scss

    use @/scss/global/functions as *

    .block {
        margin-top: size(20); // 20px
    }
    ```

* 基本テキストサイズに関わらず固定値にしたい場合
    → pxで指定

### フォントサイズ指定
* 基本は変数定義している「基本フォントサイズ」の変数で指定
    (変数指定ファイル: scss/global/variables/_fonts.scss)

* 基本フォントサイズにない場合はtext-size関数で指定
    ```
    ** pages/sample.scss

    use @/scss/global/variables as *
    use @/scss/global/functions as *

    .block p.default {
        font-size: $font-xs;
    }

    .block p.other {
        font-size: text-size(13); // 13px
    }
    ```

### tailwindCSSの併用
個別にスタイル指定するほどでもないマージン指定などは、classにtailwind追加して記載も可。

 ```
 <div class="block md:mt-10 xl:mt-20">
// mdサイズ以上はmargin-top: 10px, xlサイズ以上はmargin-top: 20px
 ```
詳細はこの辺りを参照
https://qiita.com/senju797/items/ad85794ec927f6ebd7bc

## 画像ファイル設計
### 画像ファイル名
* ファイル名の区切りはハイフンで統一
    ```
    NG: aaa_bbb_ccc.jpg
    OK: aaa-bbb-ccc.jpg
    ```
* PC用、SP用を分ける場合は末尾に[-pc / -sp]で記載
    ```
    aaa-bbb-cccc-pc.jpg
    aaa-bbb-cccc-sp.jpg
    ```
### XDからの書き出しスタイル
* ヘッダー画像など、表示サイズで1000px超えるような画像:
    → jpg, サイズx2, 画質100%で保存
* それ以外:
    → png, サイズx2で保存

## コンポーネント設計

### コンポーネント作成ツールの利用

コンポーネントの作成に下記ツールを使用すると自動作成される

```
$ symfony php bin/console app:generate:asset-component
```

* コンポーネント名 (eq. component/button/large_button) ? 

    コンポーネント名はコンポーネント管理のスプレッドシートB列「ディレクトリ/ファイル名」を参照して
    ```
    component/(ディレクトリ/ファイル名)
    ```
    で指定してください。

* Template Dir [ templates ] ? 
    →twigファイルを生成するテンプレートディレクトリを指定。そのままエンターでOK

* Scss Dir? [ assets/app/scss/object ] ? 
    →scssファイルを生成するテンプレートディレクトリを指定。こちらもそのままエンターでOK

* @forward を assets/app/scss/object/component/_index.scss に追加する？ [n] [y/n]: 
    →作成するコンポーネント用scssファイルをメインとなるCSSに読み込ませる記述をするかどうか。[y]を入力でエンター

* 生成実行しますか? 
    →本番実行でよいので[y]を入力でエンター

### コンポーネント変数名
|種類|パラメータ名|
|:----|:----|
|ボタン名|label|
|見出し|title|
|アンカーリンクテキスト|text|
|本文|text|
|リンクアドレス|url|
|画像パス|image, image_pc, image_sp|

### アンカーコンポーネントの作成ルール
* 全体がリンク要素であっても基本は最上位はdivやsectionなどのブロック要素で囲む。アンカータグはその1階層下でラップする

    ```
    <div class="c-component"> <!-- 最上位はブロック要素 -->
        <a href="#"> <!-- アンカータグはその下で -->
            <div class="c-component__image">
                <img src="#">
            </div>
            <div class="c-component__text">
                <p class="c-component__title">タイトル</p>
                <p class="c-component__text>本文が入ります。本文が入ります。</p>
            </div>
        </a>
    </div>
    ```

* ただしアンカーリンクのコンテンツがほぼテキストのみ、画像のみのような、中身が単一コンテンツの場合は最上位にアンカータグを利用してもOK

    ```
    <a href="#" class="c-button">ボタン名</a>
    とか
    <a href="#" class="c-img-link">
        <img src="#">
    </a>
    ```

### パラメータの渡し方
コンポーネント側はできるだけシンプルにして使用できる場面を広げるため、可能な限り個別変数で渡す

NGパターン：
この書き方だとvalues内でのプロパティ名が固定されてしまう
```
// NG例 呼び出し側
{% for item in items %}
    {% include component.html.twig with {
        values: item
    } %}
{% endfor %}
```

```
// NG例 コンポーネント側
<div class="c-component">
  <h2>{{ values.title }}</h2>
  <p>{{ values.text }}</p>
</div>
```

OKパターン

```
// OK例 呼び出し側
{% for item in items %}
    {% include component.html.twig with {
        title: item.title,
        text: item.text
    } %}
{% endfor %}
```

```
// OK例 コンポーネント側
<div class="c-component">
  <h2>{{ title }}</h2>
  <p>{{ text }}</p>
</div>
```

### パラメータ受け渡しの制限
includeはwithで渡さなくても親の変数を引き継ぐため、意図しない動作が発生する可能性があるため、onlyで必要なパラメータのみ渡す

```
{% include component.html.twig with {
    title: title,
    text: text
} only %}
```

### fill-slot型コンポーネントの検討
例えばhxを含むセクションの外枠だけのコンポーネントなど、何度も登場する同じスタイルの枠組みはembed関数を利用したコンポーネントを検討する

```
<section class="p-section1">
    <h2>セクション1</h2>

    …コンテンツ1…

</section>
<section class="p-section2">
    <h2>セクション2</h2>

    …コンテンツ2…

</section>
<section class="p-section3">
    <h2>セクション3</h2>

    …コンテンツ3…

</section>
```
上記のような場合で、p-section1, p-section2, p-section3のフレームは共通コンポーネントにしたい。

```
** 呼び出し側

{% embed 'fill-slot-sample.html.twig' with {
    section_name: 'section1',
    title: 'セクション1'
} %}
    {% block slot %}
        <div>
            <p>中身のコンテンツ。通常のタグでもincludeコンポーネントでも何でも書ける</p>
        </div>
    {% endblock %}
{% endembed %}
```

```
** fill-slot-sample.html.twig

<div class="c-fill-slot-sample {{ 'p-' ~ section_name }}">
    <h2>{{ title }}</h2>
    {{ block('slot') }}
</div>
```

生成されるコードは以下の通り
```
<div class="c-fill-slot-sample p-section1">
    <h2>セクション1</h2>
    <div>
        <p>中身のコンテンツ。通常のタグでもincludeコンポーネントでも何でも書ける</p>
    </div>
</div>
```

### hxタグを含むコンポーネント
レイアウトによりhxタグが固定しない場合があるため、デフォルトのhxタグは指定したうえでパラメータで変更できるようにする。

```
** component.html.twig

{# デフォルト値設定 #}
{% set h_level = h_level is defined and h_level is not null ? 'h' ~ h_level　: 'h2' %}
{# ↑意味: h_levelの変数が存在して、さらにh_levelの変数値はnullではないときは指定したh_levelのhx, それ以外はh2 #}

<div class="c-component">
  <{{ h_level }}>{{ title }}</{{ h_level }}>
  <p>{{ text }}</p>
</div>
```


### 汎用性が高すぎる変数を作らない
classがそのまま渡せる変数設計は運用上のリスクが高いため禁止

```
** NG 呼び出し側
{% include component.html.twig with {
    title: 'title',
    text: 'text text text',
    class: 'c-component--black' // 黒のmodifier
} only %}
```

```
** NG コンポーネント側
<div class="c-component {{ class }}">
  <h2>{{ title }}</h2>
  <p>{{ text }}</p>
</div>
```

意味を持った変数として渡す

```
** 呼び出し側
{% include component.html.twig with {
    title: 'title',
    text: 'text text text',
    color: 'black'
} only %}
```

```
** コンポーネント側
{% set $color_modifier = color is defined or is not null ? 'c-component--' ~ color : '' %}
<div class="c-component {{ $color_modifier }}">
  <h2>{{ title }}</h2>
  <p>{{ text }}</p>
</div>
```

### コンポーネントリストからtwigコードを参照するための表記
/componentlistに使用するtwigコードを表示するための記述

```
{% include component.html.twig with {
    title: 'title',
    text: 'text text text',
    color: 'black'
} only %}

↓ コンポーネント直下に表示するコード用ブロックを記載 ↓
<div class="code">
(twigに展開されないようにするため{% %}を{* *}に置き換え)
<!--
{* include component.html.twig with {
    title: 'title',
    text: 'text text text',
    color: 'black'
} only *}
-->
</div>
```

上記記述をしておけば、ブラウザで表示したときにJavaScriptによりclass="code"内を置き換えて表示させる