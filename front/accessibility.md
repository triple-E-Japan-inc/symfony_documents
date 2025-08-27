# アクセシビリティ対応（随時更新予定）

<div style="text-align: right;">最終更新: 2025/08/27</div>

## 大文字のみの見出し
"WORKS"など大文字のみで表示するテキストは、そのまま表記するとスクリーンリーダー（音声ブラウザ）は単語として読み上げないため、**小文字で表示＋CSSで大文字に変換**で対応します。

```html
<h2 class="headline">Works</h2>
```

```css
.headline {
　text-transform: uppercase; /* 全て大文字で表示 */
}
```

## クリックエリアの確保
クリック可能なテキスト、ボタン等は、適切な余白を入れることでクリック可能な領域を確保します。
```html
<ul class="pg-menu">
  <li><a href="#">会社概要</a></li>
  <li><a href="#">お知らせ</a></li>
  <li><a href="#">導入事例</a></li>
  <li><a href="#">お問い合わせ</a></li>
</ul>
```

```css
/* NGパターン */
.pg-menu {
  li {
    padding-block: 8px;
    text-align: center;
    a{
      font-size: 12px;
    }
  }
}

/* OKパターン */
.pg-menu {
  li {
    text-align: center;
    a {
    　font-size: 12px;
      display: block; /* liの横幅全体にクリック可能エリアを拡張
      padding-block: 8px; /* リンク領域に上下の余白を入れることでクリック可能エリアを拡張 */
    }
  }
}
```

## input要素をlabel要素で紐付け
チェックボックスなどテキスト部分とチェック部分が分かれる場合、テキスト部分をクリックしてもチェックが動作するようにします。

```html
<!-- NGパターン -->
<div>
  <input id="agree" type="checkbox" value="1">同意する
</div>

<!-- OKパターン1: label要素で囲う -->
<label>
  <input type="checkbox" value="1">同意する
</label>

<!-- OKパターン2: for属性で紐付け -->
<input id="agree" type="checkbox" value="1"><label for="agree">同意する</label>
```

## アイコンのみのボタンの適切なラベル付与
中身のテキストがないボタン（=アイコンのみCSSで設定している場合など）はスクリーンリーダーで適切な読み上げがされないため、aria-label属性で情報を補足します。

```html
<!-- NGパターン -->
<button></button>

<!-- OKパターン: aria-label属性を記述 -->
<button aria-label="検索する"></button>

<!-- OKパターン: 中身のテキストがあるものは属性付与は不要 -->
<button>検索する</button>
```
## button要素のポインター表示
button要素はデフォルトではマウスオンでもカーソルが変化しないため、CSSのcursor:pointerを記載して指差しアイコンにします。

```css
button {
  cursor: pointer;
}
```
