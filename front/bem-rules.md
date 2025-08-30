# コーディングルール(BEM記法)

## 命名について
### Elementの区切り文字はアンダーバー2つ
block__element

### Modifierはクラス名で定義せずdata属性を使う
例)
ｰ NG: <div class="block__element--modifier-value">
- OK: <div class="block__element" data-modifier="value">

### クラス名で使う単語は省略形にしない
例）
- NG: img  OK: image
- NG: btn  OK: button

### Block, Elementの単語区切りはキャメルケース、スネークケースは使わずケバブケースで書く（単語の繋ぎをハイフンで書く）
c-news-block__image-area--color-black

参考:
https://www.wakuwakubank.com/posts/804-it-naming-convention/

### 使用する単語は英語表記の正確さよりも理解しやすさを優先する
例）　
職種: occupation → job

## HTML
### Elementは複数個繋げない

```eample01.html
[NG]
<div class="news">
  <div class="news__list">
    <div class="news__list__item">　← × listとitemの両方がエレメント

[OK]
<div class="news">
  <div class="news__list">
    <div class="news__item">
もしくは
<div class="news">
  <div class="news-list">　←blockに昇格
    <div class="news-list__item">
```

### Elementはできるだけ1単語にして、親エレメントと繋げた2単語にしない
複数個エレメントと同じ扱いになってしまうため。
ただし2単語以上で1つの意味を持つ場合はOK

```example02.html
[NG]
<div class="block__list">
  <div class="block__list-item">

[OK]
<div class="block__list">
  <div class="block__item">

<div class="block__list">
  <div class="block__item">
    <div class="block__image-area">　← 2単語以上で1つの意味
    </div>
    <div class="block__text-area">
    </div>
```

### block内blockを過剰に作らない
block内blockはSCSS記述の際のまとまりがなくなるため。
HTML構造的な親子関係を意識しすぎず、ブロックに対してそれを構成するエレメントであることを意識する。

```example03.html
[NG]
<div class="block">
  <div class="block-headline">
    <div class="block-headline__title">見出し</div>
    <div class="block-headline__subtitle">サブタイトル</div>
  </div>
  <div class="block__content">
  </div>
</div>

[OK]
<div class="block">
  <div class="block__headline">
    <div class="block__title">見出し</div>
    <div class="block__subtitle">サブタイトル</div>
  </div>
  <div class="block__content">
  </div>
</div>
```

### block内blockは親ブロックの名称を引き継いで関連性がわかりやすい名称にする
（block内blockがコンポーネントの場合は除外)

```example04.html
[NG]
<div class="shop-article">
  <div class="shop-pictures"> ←shop-articleを構成するblock養成であることが分からない
   
[OK]
<div class="shop-article">
  <div class="shop-article-pictures">
```

## SCSS

### Elementは&の連結を使ってブロックが分かりやすいように書く

```example01.scss
.block{
  &__element {

  }
}
```

### SCSSでblock内blockを書くときは内包せずに書く
```example02.scss
/* [NG] */
.news{
  &__list {
    .news-list{  // 内包block
      &__item{
       …
      }
    }
  }
}

/* [OK] */
.news{
  &__list {
  }
}
.news-list{
  &__item{
   …
   }
}
```

### \@extendは使わず他の方法（\@mixin, \@includeなど）を使う。ただしプレスホルダーによる記述はOK

```example03-ng.scss
/* [NG] */
.news-list{
  …
}
.blog-list{
  @extend .news-list;
}
```

```example03-ok01.scss
/* [OK] 共通の内容をプレースホルダーで定義 */
// 
%_item-list{
  …
}
.news-list{
  @extend %_item-list;
}
.blog-list{
  @extend %_item-list;
}
```

```example03-ok02.scss
/* [OK] @mixin, @includeを利用 */
@mixin _item-list{
  …
}
.news-list{
  @include _item-list;
}
.blog-list{
  @include _item-list;
}

```

### スタイル定義をするタグはできるだけclass指定する

```example04-ng.html
[NG]
<div class="block">
  <div class="block__element01">
    <ul>
      …
    </ul>
  </div>
</div>
```
```example04-ng.scss
/* [NG] */
.block{
  &__element01{
    ul{
      …
    }
  }
}
```

```example04-ok.html
[OK]
<div class="block">
  <div class="block__element01">
    <ul class="block__list">
      …
    </ul>
  </div>
</div>
```
```example04-ok.scss
/* [OK] */
.block{
  &__element01{
  }
  &__list{
    …
  }
}
```
