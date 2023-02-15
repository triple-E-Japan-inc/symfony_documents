# コーディングルール(BEM記法)

## 命名について
### Elementの区切り文字はアンダーバー2つ
block__element

### Modifierの区切り文字はハイフン2つ
block__element--modifier

### クラス名で使う単語は省略形にしない
例）　
- ×: img  ○: image
- ×: btn  ○: button

### Block, Element, Modifierの単語区切りはキャメルケース、スネークケースは使わずケバブケースで書く（単語の繋ぎをハイフンで書く）
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

### ModifierのマルチクラスはOKとする
シングルクラス縛りにするとscssの記述が分かりにくくなり可読性が下がるため

シングルクラス縛りにするとこんな感じでscssがちょっと複雑になる

```example05a.html
メニューが閉じているとき
<div class="global-menu">
メニューが開いているとき
<div class="global-menu-―is-open">
```

```example05a.scss
// この書き方だとNG
.global-menu{
  // メニューの基本スタイル
  &--is-open{
    // メニューopen時のスタイル
    // ! ここに[メニューの基本スタイル]が引き継がれない !
  }
}

// ↓　↓　↓ //

// プレースホルダーでメニューの基本スタイルを定義してそれぞれの状態に渡す
%_global-menu{ 
  // メニューの基本スタイル
}
.global-menu{
  @extend _global-menu;
  &--is-open{ // 開いたとき
    @extend _global-menu;
    // メニューopen時のスタイル
  }
}
```

マルチクラスで記述するとCSSの可読性が高くなり、CSS基本的な継承機能も生かせる


```example05.html
メニューが閉じているとき
<div class="global-menu">
メニューが開いているとき
<div class="global-menu global-menu-―is-open">
```
```example05.scss
.global-menu{
  // メニューの基本スタイル
  &--is-open{
    // メニューopen時のスタイル
  }
}
```

## SCSS

### Element, Modifierは&の連結を使ってブロックが分かりやすいように書く

```example01.scss
.block{
  &__element {
    &—-modifier {

    }
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

### blockにmodifierを指定してelementを変更させる方法
```element05.html
blockのmodifierでtype01を指定した場合はcategoryとtextの背景色を変えたい

<div class="block block--type01">
  <p class="block__category">カテゴリ01</p>
  <p class="block__text">テキストテキストテキストテキスト</p>
</div>
```

```element05-ng.scss
.block{
  &__category{
    background-color: #aa0000;
  }
  &__text{
    background-color: #eeffff;
  }
  &--type01{
    &__category{
      background-color: #00aa00;
      // .block__type01　.block__categoryを指定したいが
      // .block__type01--categoryクラスへの指定となり、変更が上書きされない
    }
    &__text{
      background-color: #ffeeff;
      // 上記と同様
    }
  }
}
```

block名を変数に割り当てることで効率的に書ける

```element05-ok.scss
.block{
  $_block: &; // block名を変数_blockに割当て
  &__category{
    background-color: #aa0000;
  }
  &__text{
    background-color: #eeffff;
  }
  &--type01{
    #{$_block}{
      &__category{
        background-color: #00aa00;
        // .block__type01 .block__categoryクラスを指定
      }
      &__text{
        background-color: #ffeeff;
        // .block__type01 .block__textクラスを指定
      }
    }
  }
}
