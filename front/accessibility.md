# アクセシビリティ対応

## 大文字のみの見出し
"WORKS"など大文字のみで表示するテキストは、そのまま表記するとスクリーンリーダー（音声ブラウザ）は単語として読み上げないため、**小文字で表示＋CSSで大文字に変換**で対応する。

```html
<h2 class="headline">Works</h2>
```

```css
.headline {
　text-transform: uppercase;
}
```

## クリックエリアの確保
クリック領域が狭いテキスト等は、適切な余白を入れることでクリッカブル領域を確保します。

```css
a.link {
　font-size: 12px;
  padding-block: 4px; /* 上下の余白を入れることでクリック可能エリアを拡張 */
}
```

## input要素をlabel要素で紐付け
チェックボックスなどテキスト部分とチェック部分が分かれる場合、テキスト部分をクリックしてもチェックが動作するようにします。

```html
<!-- OKパターン1: label要素で囲う -->
<label>
  <input type="checkbox" value="1">同意する
</label>

<!-- OKパターン2: for属性で紐付け -->
<input id="agree" type="checkbox" value="1"><label for="agree">同意する</label>
```

## アイコンのみのボタンの適切なラベル付与
アイコンのみボタンはスクリーンリーダーで適切な読み上げがされないため、aria-label属性で情報を補足します。

```html
<!-- NGパターン -->
<button></button>

<!-- OKパターン: aria-label属性を記述 -->
<button aria-label="検索する"></button>
```
## button要素のポインター表示
button要素はデフォルトではマウスオンでもカーソルが変化しないため、CSSのcursor:pointerを記載して指差しアイコンにします。

```css
button {
  cursor: pointer;
}
```
